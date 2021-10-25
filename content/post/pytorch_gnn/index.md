---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Pytorch Geometric 1. Massage Passing"
subtitle: ""
summary: ""
authors: []
tags: 
- Pytorch
- Graph Neural Network
date: 2021-10-24T22:36:45-04:00
lastmod: 2021-10-24T22:36:45-04:00
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---


2 Classes you need to self define when you implement Graph Neural Network(GNN):
- MyNet(pytorch.nn.Moduel)
- MyGraphModel(torch_geometric.nn.MessagePassing)


# MyNet(pytorch.nn.Moduel)

In your overall model structure, you should implement:
- (in `__init__`): call a MessagePassing child class to build massage-passing model
- (in `forward`):
  - make sure the **data** follows the requirement of MessagePassing child class
  - do the "**iterative massage passing**"(K-times) in `forward`, the final output will be the node embedding you need.

```python

class MyNet(nn.Module):
  del __init__():
    super(MyNet, self).__init__
    # other building blocks of the Net
    ...
    # GNN part (General)
    conv_model = self.build_conv_model(args.model_type) #
    self.convs = nn.ModuleList()
    self.convs.append(conv_model(input_dim, hidden_dim))
    assert (args.num_layers >= 1), 'Number of layers is not >=1'
    for l in range(args.num_layers-1):
        self.convs.append(conv_model(args.heads * hidden_dim, hidden_dim)) 

  
    def build_conv_model(self, model_type):
        if model_type == 'GraphSage':
            return GraphSage
        elif model_type == 'GAT':
            # When applying GAT with num heads > 1, one needs to modify the 
            # input and output dimension of the conv layers (self.convs),
            # to ensure that the input dim of the next layer is num heads
            # multiplied by the output dim of the previous layer.
            # HINT: In case you want to play with multiheads, you need to change the for-loop when builds up self.convs to be
            # self.convs.append(conv_model(hidden_dim * num_heads, hidden_dim)), 
            # and also the first nn.Linear(hidden_dim * num_heads, hidden_dim) in post-message-passing.
            return GAT

    def forward(self, data):
        x, edge_index, batch = data.x, data.edge_index, data.batch
          
        for i in range(self.num_layers):
            x = self.convs[i](x, edge_index)
            x = F.relu(x)
            x = F.dropout(x, p=self.dropout)

        x = self.post_mp(x)

        if self.emb == True:
            return x

        return F.log_softmax(x, dim=1)

    def loss(self, pred, label):
        return F.nll_loss(pred, label)



```

# MSG Passing

The Second Step is define the `message passing mechanism`:
- forward
- message
- aggregate (if the aggregator is given by pyg, it could be implement as a argument pass to super class)


## GraphSage

$$
\begin{array}{l}
h_{v}^{(l)}=W_{t} \cdot h_{v}^{(l-1)}+W_{r} \cdot A G G\left(\left\{h_{u}^{(l-1)}, \forall u \in N(v)\right\}\right) \\
A G G\left(\left\{h_{u}^{(l-1)}, \forall u \in N(v)\right\}\right)=\frac{1}{|N(v)|} \sum_{u \in N(v)} h_{u}^{(l-1)}
\end{array}
$$

in graph sage:
- aggregator: SUM
- normalizer: same for every neighbors, $\frac{1}{|N(v)|}$.


```python


class GraphSage(MessagePassing):
    
    def __init__(self, in_channels, out_channels, normalize = True,
                 bias = False, **kwargs):  
        super(GraphSage, self).__init__(**kwargs)

        self.in_channels = in_channels
        self.out_channels = out_channels
        self.normalize = normalize

        self.lin_l = nn.Linear(self.in_channels, self.out_channels) # linear transformation that you apply to embedding  for central node.
             
        self.lin_r = nn.Linear(self.in_channels, self.out_channels) # linear transformation that you apply to aggregated(already) info from neighbors.

        self.reset_parameters()

    def reset_parameters(self):
        self.lin_l.reset_parameters()
        self.lin_r.reset_parameters()      

    def forward(self, x, edge_index, size = None):
        prop = self.propagate(edge_index, x=(x, x), size=size) # see Messsage.Passing.propagate() in https://pytorch-geometric.readthedocs.io/en/latest/
        out = self.lin_l(x) + self.lin_r(prop)
        if self.normalize:
          out = F.normalize(out, p=2)
        
        return out
    
    # Implement your message function here.
    def message(self, x_j):
      out = x_j
      return out
    
    # Implement your aggregate function here.
    def aggregate(self, inputs, index, dim_size = None):
        # The axis along which to index number of nodes.
        node_dim = self.node_dim
        # since 
        out = torch_scatter.scatter(inputs, index, node_dim, dim_size=dim_size, reduce='mean') # see https://pytorch-scatter.readthedocs.io/en/latest/functions/scatter.html#torch_scatter.scatter
        return out

```


Then is the attention mechanism, which shows promising performance in many sequence-based tasks.