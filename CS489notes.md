<script type="text/javascript">
  var fileref=document.createElement('script')
  fileref.setAttribute("type","text/javascript")
  fileref.setAttribute("src", "https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.2/MathJax.js?config=TeX-AMS_CHTML-full")
  document.getElementsByTagName("head")[0].appendChild(fileref)
</script>


# CS 489: Neural Networks

```
Notes by Shaw Tan
```

\n
# Supervised Learning

## Learning
1. **Supervised Learning**:
    - Output is known
    - Can compute error and use it to adjust the network
2. **Unsupervised Learning**:
    - Output unknown
    - Learning by finding efficient representations on the input
    - Bottleneck layer?
3. **Reinforcement Learning**:
    - General feedback given
    - e.g Win or loss in chess

## Supervised Learning
- Neural network is a map from input space to output space
- We feed the network input and target, and want to get the weights close to the target

### Mappings
1. **Regression**
    - Output is continuous in a range
2. **Classification**
    - Output is in categories
    - e.g MNIST

### Optimization
- Learning with a cost function is just an optimization problem
- We want to find the weights and biases that minimize the expected cost between output and target


Let $\vec{x}$ be the input  
Let $\theta$ be the weights and biases  
Let $\vec{t}$ be the target  
Let $\vec{y} = f(\vec{x}; \theta)$ be the network mapping  

$$
\min_\theta E \left[ E(f(\vec{x};\theta), \vec{t}(\vec{x})) \right]
$$

