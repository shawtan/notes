<script type="text/javascript">
    (function () {
      var script = document.createElement("script");
      script.type = "text/javascript";
      script.src  = "https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.2/MathJax.js?config=TeX-AMS_CHTML-full";
      document.getElementsByTagName("head")[0].appendChild(script);
    })();
</script>
# CS 489: Neural Networks

```
Notes by Shaw Tan
```

# Neuron Models

## The Neuron
![neuron_membrane](neuron_membrane.png)

- *Ions* are molecules or atoms that carry a charge 
    - the number of electrons do not match the number of protons
- Cell membrane contains ion channels that allow ions through
    - Sodium Channel: Na+ into cell
    - Potassium Channel: K+ out of cell
    - Sodium-Potassium Pump: 2K+ in, 3N+ out
- **Membrane Potential**: voltage difference across the membrane
- **Action Potential**: spike of electrical activity that travels along the **axon** to the **synapses**. The *synapses* pass signals to other neurons

## Hodgkin-Huxley Model
- Model  of spikes based on non-linear interactions between voltage and ion channels
- Problem: Takes many time steps to model a spike

Let $V$ be the membrane potential (usually kept around -10mV)  
Let $n$ be the probability that one (of four) K+ gates are open  
Let $t_n(V)$ be the time constant  
Let $n_\infty(V)$ be the equilibrium solution constant  

The fraction of K+ channels open is $n^4$
$$
\frac{d n}{d t} = \frac{1}{\tau_n(v)}(n_\infty(v) - n)
$$

The fraction of Na+ ion channels open is $m^3h$
$$
\frac{d m}{d t} = \frac{1}{\tau_m(v)}(m_\infty(v) - m)
$$
$$
\frac{d h}{d t} = \frac{1}{\tau_h(v)}(h_\infty(v) - h)
$$

Let $J_{in}$ be the input current 

The change in V is then
$$
C \frac{dV}{dt} = J_{in} - g_L(V-V_L) - g_{Na}m^3h(V - V_{Na}) - g_Kn^4(V-V_K)
$$

The following graph shows how V changes with current  
<img src="voltage_demo-J.png" align="left" width="50%"/><img src="voltage_demo-V.png" align="left" width="50%"/>
<!-- ![voltage_demo-J](voltage_demo-J.png) ![voltage_demo-V](voltage_demo-V.png) -->

## Leaky Integrate-and-Fire (LIF) Model
- Only considers the sub-threshold membrane potential
- Does not model voltage during spike
- Only records when spikes occur
- TODO: figure out this math

$$\tau_m \frac{d v}{d t} = v_{in} - v$$

where $v<1$

Also, $v(t) = v_{in} (1 - e^{\frac{-t}{r_m}})$ is a solution to $\tau_m \frac{d v}{d t} = v_{in} - v$

## Activation Functions
![activation_functions](activation_functions.png)

## Synapses
- In a network, input comes from other neurons
- **Synapse**: The junction where one neuron communicates with the next
- Synaptic process:
    1. Pre-synaptic action potential (travels up axon)
    2. Cause release of neurotransmitters
    3. Neurotransmitters bind to receptors on the post-synaptic neuron
    4. Opens (or closes) ion channels, changing the membrane potential
- Some synapses are fast (10ms), and some are slow (300ms)

The Post-Synaptic Current (PSC) can be written as 
$$
h(t) = \begin{cases}
    kt^ne^{\frac{-t}{\tau_s}} & \text{if $t \geq 0$} \\
    0   & \text{if $t < 0$}
\end{cases}
$$

### Spiking
- Multiple spikes form a *spike train* which can be modelled as a sum of Dirac delta function
![filtered_spike_train](filtered_spike_train.png)

## Connection Weight
- The total current induced by an action potential depends on:
    1. Number and sizes of synapses
    2. Amount and type of neurotransmitter
    3. Number and type of receptors
- **Connection Weight**: combined factors influencing input to a neuron
- We use **weight matrices** to store connection weights between neurons
- TODO: figure out math

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
    - Use a *linear* activation function (identity, threshold) with *MSE* loss function
2. **Classification**
    - Output is in categories
    - e.g MNIST
    - Use *logistic* activation function with *cross entropy* loss function

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

## Loss Functions
- Quantifies how close the output is to the target

Let $\vec{x}$ be the input  
Let $\vec{t}$ be the target  
Let $\vec{y}$ be the output of our network  

### Mean Squared Error (MSE)
$$
E(\vec{y}, \vec{t}) = \frac{1}{N} \sum_{i=1}^N \vert y_i - t_i \vert_2^2
$$

- Often used with linear activation functions or ReLu because they afford a larger output range

### Cross Entropy
- Works based on probability magic with Bernoulli distributions (TODO)
- Assumes that output values are in the range $[0,1]$

$$
E(\vec{y}, \vec{t}) = - \sum_{i=1}^N t_i\ln y_i + (1-t_i)\ln(1-y_i)
$$

### Softmax
$$
\text{softmax}(z)_i = \frac{e^{z_i}}{\sum_j e^{z_j}} %_
$$

### One-hot
- The largest element is set to 1, and the others 0

## Perceptrons
We come up with weights so that 
$y = \vec{W}\vec{x} + b$
matches the target.

This is a linear equation so it only works for *linearly separable data*.

## Gradient Descent Learning
Let $\vec{y} = f(\vec{x}; \theta)$ be the operation of the network  
Let $E(\vec{y}, \vec{t})$ be the cost function  

Neural learning is the optimization problem 
$$
\min_\theta E \left[ E(f(\vec{x};\theta), \vec{t}(\vec{x})) \right]
$$

The gradient of $E$ is
$$
\nabla_\theta E = \left[\frac{\partial E}{\partial \theta_0}, \frac{\partial E}{\partial \theta_1}, \dots, \frac{\partial E}{\partial \theta_p}\right]^T
$$

In gradient descent, you want to *minimize* the objective function by stepping in the *opposite* direction of the gradient vector.

The partial derivatives can be estimated using finite differencing:
$$
\frac{d f}{d \theta} \approx \frac{f(\theta + \delta \theta) - f(\theta - \delta \theta)}{2 \delta \theta}
$$

We update the variable by $\text{LR} \times -\frac{\partial E}{\partial \theta_1}$

## Error Backpropagation
To apply gradient descent on a multi-layer network, we need to calculate the gradients of the error with respect to the deeper connection weights and biases.


Consider a 3 layer network with input nodes $x_1, x_2, x_3$, hidden nodes $h_1, h_2, h_3, h_4$, and output nodes $y_1, y_2$.
![network](error_backprop_network.png)

Let $\alpha = W\vec{x} + \vec{a}$ be the input current to $h$  
Let $\beta = M\vec{h} + \vec{b}$ be the input current to $y$  

To calculate $\frac{\partial E}{\partial M_{14}}$,
$$
E (\vec{y}, \vec{t}) = E(\sigma(\vec{\beta}), \vec{t})
$$
$$
\beta_1 = \sum_{i=1}^4 M_{1i} h_i + b_1
$$
$$
\begin{align*} %*
\frac{\partial E}{\partial M_{14}} &= \frac{\partial E}{\partial \beta_1} \frac{\partial \beta_1}{\partial M_{14}} \\
\frac{\partial \beta_1}{\partial M_{14}} &= \frac{d}{d M_{14}} \sum_{i=1}^4 M_{1i}h_i + b_1 \\
    &= h_4 \\
\frac{\partial E}{\partial \beta_1} &= \frac{\partial E}{\partial y_1} \frac{d y_1}{d \beta_1} \\ 
    &= \frac{\partial E}{\partial y_1} \sigma'(\beta_1) \\ 
\implies \frac{\partial E}{\partial M_{14}} &= \frac{\partial E}{\partial y_1} \frac{d y_1}{d \beta_1} h_4 \\
\end{align*} %*
$$

To calculate $\frac{\partial E}{\partial W_{12}}$,
$$
E(\vec{y}, \vec{t}) = E(\sigma(\vec{\beta}), \vec{t}) = E(\sigma(M\vec{h} + \vec{b}), \vec{t})
$$
$$
\vec{h} = \sigma(\vec{\alpha})
$$
$$
\alpha_1 = \sum_{i=1}^3 W_{1i} x_i + a_1
$$
$$
\begin{align*} %*
\frac{\partial E}{\partial W_{12}} &= \frac{\partial E}{\partial \alpha_1} \frac{\partial \alpha_1}{\partial W_{12}} \\
\frac{\partial \alpha_1}{\partial W_{12}} &= \frac{d}{d \alpha_1} \sum_{i=1}^3 M_{1i}x_i + a_1 \\
    &= x_2 \\
\frac{\partial E}{\partial \alpha_1} &= \frac{d h_1}{d \alpha_1} \frac{\partial E}{\partial h_1} \\ 
    &= \sigma'(\alpha_1) \left(\sum_{i=1}^2 \frac{\partial \beta_i}{\partial h_1} \frac{\partial E}{\partial \beta_i} \right)\\
    &= \sigma'(\alpha_1) \left(\sum_{i=1}^2 M_{i1}\frac{\partial E}{\partial \beta_i}\right) \\
\implies \frac{\partial E}{\partial W_{12}} &= \sigma'(\alpha_1) \begin{bmatrix}M_{11} & M_{21}\end{bmatrix}\begin{bmatrix}\frac{\partial E}{\partial \beta_1} \\ \frac{\partial E}{\partial \beta_2}\end{bmatrix}\\
\end{align*} %*
$$

*In general*, for any layer,

Let $\vec{z}^{(l+1)} = W^{(l+1)}\vec{z}^{(l)} + \vec{b}^{(l+1)}$ be the input current to layer $l$  
Let $\vec{h}^{(l)} = \sigma(z^{(l)})$ be the activations at layer $l$  

$$
\frac{\partial E}{\partial z^{(l)}} = \frac{d h^{(l)}}{d z^{(l)}} \odot \left(W^{(l)}\right)^T \frac{\partial E}{\partial z^{(l+1)}}
$$

$$
\frac{\partial E}{\partial W^{(l)}} = \frac{\partial E}{\partial \vec{z}^{(l+1)}} \frac{d \vec{z}^{(l+1)}}{d W^{(l)}} = \frac{\partial E}{\partial \vec{z}^{(l+1)}} \vec{h}^{(l)}
$$

$$
\frac{\partial E}{\partial \vec{b}^{(l)}} = \frac{\partial E}{\partial \vec{z}^{(l)}} \frac{d \vec{z}^{(l)}}{d \vec{b}^{(l)}} = \frac{\partial E}{\partial \vec{z}^{(l)}}
$$

#### Hadamard Product
$$
\begin{bmatrix}
a & b
\end{bmatrix}
\odot
\begin{bmatrix}
c & d
\end{bmatrix}
= 
\begin{bmatrix}
ac & bd
\end{bmatrix}
$$

## Training and Testing
- Data is broken up into
    - Training set
    - Validation set: taken from the training set and used to evaluate performance
    - Test set: used to evaluate the model
- Avoids inflated accuracy from overfitting 
    - **Overfitting**: When the model starts to fit the noise specific to the  training set, instead of the underlying model
- Every passthrough of the training data is an **epoch**

## Preventing Overfitting
- A complex model (more degrees of freedom) can be hyper-adapted to the training set and start to fit the noise
    - This makes the training error *very* small
- Problematic because the model will not generalize to new samples
    - Test error will be much bigger than training error
- There are **regularization** techniques to prevent overfitting

### Weight Decay
- Add magnitude of weights to loss function
- Creates preference for solutions with smaller weights
$$
\tilde{E}(\vec{y}, \vec{t}; \theta) = E(\vec{y}, \vec{t}) + \lambda \vert \theta \vert^2_F
$$
$$
\frac{\partial \tilde{E}}{\partial \theta_i} = \frac{\partial E}{\partial \theta_i} + 2\lambda \theta_i
$$

We usually usually choose $\lambda = K / C$

#### Frobenius Norm
$$\vert \theta \vert^2_L = \sum_{i} \theta^2_i$$
Frobenius Norm is a matrix norm defined as the square root of the sum of the absolute squares of its elements.

We can also use the *$L_1$ norm*
$$
L_1(\theta) = \sum_i \vert \lambda_i \vert
$$
The $L_1$ norm favours sparsity where most weights are 0.

### Data Augmentation
- Include a wider variety of samples so that noise is less of a factor
- For image input, we can *shift* or *rotate* the images (as long as it does not change the label)

### Dropout
- In an iteration, ignore a large portion (usually 50%) of the hidden nodes
- Set those nodes to 0 for that iteration
- We need to proportionally increase the signal $z$ to compensate for the reduced output
- TODO: math to explain dropout
- Similar to training a lot of different networks 
- Network needs to find a model that is robust to loss of nodes

![dropout_network](dropout_network.png)

Let $m^i$ be a mask on layer $i$  

Learning will work as follows:

Feedforward:
$$
\begin{align*} %*
z^1 &= W^0x+b^1 \\
\hat{h}^1 &= \sigma(z^1) \\
\end{align*} %*
$$

> Apply mask:

$$
\begin{align*} %*
h^1 &= \hat{h}^i \odot m^1 \\
\end{align*} %*
$$

> Double weights to compensate for the hidden nodes:

$$
\begin{align*} %*
z^2 &= 2W^2h^1 + b^2
\end{align*} %*
$$ 

Backprop:
$$
\begin{align*} %*
\frac{\partial E}{\partial z^1} 
    &= \frac{\partial 2h^1}{\partial z^1} \odot (W^1)^T \frac{\partial E}{\partial z^2} 
    = 2\frac{\partial h^1}{\partial z^1} \odot (W^1)^T \frac{\partial E}{\partial z^2} \\
\frac{\partial E}{\partial W^1} 
    &= \frac{\partial z^2}{\partial W^1} \frac{\partial E}{\partial z^2} 
    = 2h^1 \frac{\partial E}{\partial z^2}
\end{align*} %*
$$

*Generally*, 
Let $d$ be the proportion of nodes kept, $0 < d \leq 1$

Feedforward:
$$
\begin{align*} %*
z^1 &= W^0x+b^1 \\
h^{(i)} &= \sigma(z^{(i)}) \odot m^{(i)} \\
z^{(i+1)} &= \frac{1}{d}W^{(i)}x+b^{(i+1)} \\
\end{align*} %*$$

Backprop:
$$
\begin{align*} %*
\frac{\partial E}{\partial z^{(i)}} 
    &= \frac{\partial E}{\partial z^{(i+1)}} \left(\frac{1}{d} (W^{(i)})^T \right)\odot \frac{\partial h^{(i)}}{\partial z^{(i)}} \\
\frac{\partial E}{\partial W^{(i)}} 
    &= \frac{1}{d} \frac{\partial E}{\partial z^{(i+1)}} \frac{\partial h^{(i)}}{\partial z^{(i)}}
\end{align*} %*
$$


## Deep Neural Networks

### Universal Approximation Theorem
Let $I_m$ be the $m$-dimensional unit hypercube $[0,1]^m$  
Let $C(I_m)$ be the space of continuous functions on $I_m$  

Then, given any $\epsilon > 0$ for any function $f \in C(I_m)$, integer $N$, real constants $v_i, b_i \in \mathbb{R}$, real vectors $w_i \in \mathbb{R}^m$, we can define
$$F(x) = \sum_{i=1}^N v_i \phi(w_i^Tx + b_i)$$
such that
$$\vert F(x) - f(x) \vert < \epsilon$$

So $F$ is an approximation of $f$.

- Thus, any real-valued, continuous, function can be approximated by a single-layer neural network.
- However, the number of hidden nodes needed grows exponentially, so deeper networks are preferred
- Deeper networks run into problems too

#### Vanishing Gradient Problem
- When the current is large in magnitude, the slope of the activation function (for sigmoid and tanh) gets very close to 0.
- As a result, $\frac{\partial E}{\partial z} \approx 0$ 
- The gradient gets smaller the deeper you go and there's no learning
- e.g In a network with 4 hidden layers of one node each
    - $\frac{\partial E}{\partial z^{(4)}} = (y - t)w^{(4)} \sigma'(z^{(4)})$
    - $\frac{\partial E}{\partial z^{(1)}} = (y - t) \prod_{i=1}^4 w^{(i)} \sigma'(z^{(i)})$
    - Since $\sigma(i) \leq 0.25$, the gradient will decrease by at least a factor of 4 every layer

#### Exploding Gradient Problem
- When there are large weights and biases such that $z \approx 0$, the $\frac{\partial E}{\partial z}$ compound in each layer and become large
- Rare because weights and biases need to compensate to get into the sweet spot

## Enhancing Optimization
### Batch Gradient Descent
- Data: array of (input, target) pairs $[(\vec{x}_1, \vec{t}_1), \dots, (\vec{x}_\mathbb{p}, \vec{t}_\mathbb{P})]$
- We can put them into matrices $X = [\vec{x}_1 \dots \vec{x}_\mathbb{P}]$ and $T = [\vec{t}_1 \dots \vec{t}_\mathbb{P}]$
- This allows us to process all $\mathbb{P}$ inputs in one calculation
- Problem: processing the entire database for each update can be slow

### Stochastic Gradient Descent
- Randomly choose 'mini-batches'  from our dataset to use as input
- More stable than single-sample, and faster than full-dataset

### Momentum
- If the gradient is in a half-pipe shape, gradient descent can be inefficient since you keep going back and forth along the walls
- We can add momentum to smooth out the trajectory
- This helps smooth out oscillations and prevent getting stuck in local minima

Let $V = \frac{d D}{d t}$ be the velocity  
Let $A = \frac{d V}{d t}$ be the acceleration  

By Euler's method,
$$D_{n+1} = D_n + \delta t V_n$$
$$V_{n+1} = (1-v)V_n + \delta t A_n$$
$v \in (0,1)$ is the resistance (friction).

The distance formula is similar to our weight update formula
$$W_{n+1} = W_n - \kappa\frac{\partial E}{\partial W_n}$$

Instead of $V = \frac{\partial E}{\partial W_n}$, we let $A = \frac{\partial E}{\partial W_n}$.
$$\implies V_{n+1} = (1-r)V_n - \frac{\partial E}{\partial W} = \beta V_n + (1-\beta)\frac{\partial E}{\partial W}$$ 
$$W_{n+1} = W_n - \kappa V_{n+1}$$

