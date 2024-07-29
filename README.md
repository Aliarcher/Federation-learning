# Federation-learning
![72083f76-475f-4a38-b6b8-c29d664354e5_1108x598](https://github.com/user-attachments/assets/f082db7e-59a0-4af8-810c-5f3d9226eaf0)

It enables machine learning on distributed data by moving the training to the data, instead of moving the data to the training. Here’s the single-sentence explanation:

Central machine learning: move the data to the computation

Federated (machine) learning: move the computation to the data

By doing so, it enables us to use machine learning (and other data science approaches) in areas where it wasn’t possible before. We can now train excellent medical AI models by enabling different hospitals to work together. We can solve financial fraud by training AI models on the data of different financial institutions. We can build novel privacy-enhancing applications (such as secure messaging) that have better built-in AI than their non-privacy-enhancing alternatives. And those are just a few of the examples that come to mind. As we deploy federated learning, we discover more and more areas that can suddenly be reinvented because they now have access to vast amounts of previously inaccessible data.

So how does federated learning work, exactly? Let’s start with an intuitive explanation.

## Federated learning in five steps

### Step 0: Initialize global model

We start by initializing the model on the server. This is exactly the same in classic centralized learning: we initialize the model parameters, either randomly or from a previously saved checkpoint.

<img width="757" alt="fl-initialize-global-model" src="https://github.com/user-attachments/assets/8442331d-bafd-48e3-b0bf-0a9b89e5cd79">

### Step 1: Send model to a number of connected organizations/devices (client nodes)
Next, we send the parameters of the global model to the connected client nodes (think: edge devices like smartphones or servers belonging to organizations). This is to ensure that each participating node starts their local training using the same model parameters. We often use only a few of the connected nodes instead of all nodes. The reason for this is that selecting more and more client nodes has diminishing returns.

<img width="770" alt="fl-send-global-model" src="https://github.com/user-attachments/assets/3f6e7b7e-e7a0-4f0c-bb9c-0eef78f550c0">

### Step 2: Train model locally on the data of each organization/device (client node)
Now that all (selected) client nodes have the latest version of the global model parameters, they start the local training. They use their own local dataset to train their own local model. They don’t train the model until full convergence, but they only train for a little while. This could be as little as one epoch on the local data, or even just a few steps (mini-batches).

<img width="957" alt="fl-local-training" src="https://github.com/user-attachments/assets/fa3616bd-4f1e-4fe6-8f7e-e4830b7b8d90">


### Step 3: Return model updates back to the server
After local training, each client node has a slightly different version of the model parameters they originally received. The parameters are all different because each client node has different examples in its local dataset. The client nodes then send those model updates back to the server. The model updates they send can either be the full model parameters or just the gradients that were accumulated during local training.

<img width="949" alt="fl-send-model-updates" src="https://github.com/user-attachments/assets/9b9233c2-6c5d-44a3-bcb3-8521003911b1">


### Step 4: Aggregate model updates into a new global model
The server receives model updates from the selected client nodes. If it selected 100 client nodes, it now has 100 slightly different versions of the original global model, each trained on the local data of one client. But didn’t we want to have one model that contains the learnings from the data of all 100 client nodes?

In order to get one single model, we have to combine all the model updates we received from the client nodes. This process is called aggregation, and there are many different ways to do it. The most basic way to do it is called Federated Averaging (McMahan et al., 2016), often abbreviated as FedAvg. FedAvg takes the 100 model updates and, as the name suggests, averages them. To be more precise, it takes the weighted average of the model updates, weighted by the number of examples each client used for training. The weighting is important to make sure that each data example has the same “influence” on the resulting global model. If one client has 10 examples, and another client has 100 examples, then - without weighting - each of the 10 examples would influence the global model ten times as much as each of the 100 examples.

<img width="681" alt="fl-aggregate-model-updates" src="https://github.com/user-attachments/assets/dceda822-3214-4122-83db-9521f1304b58">

### Step 5: Repeat steps 1 to 4 until the model converges
Steps 1 to 4 are what we call a single round of federated learning. The global model parameters get sent to the participating client nodes (step 1), the client nodes train on their local data (step 2), they send their updated models to the server (step 3), and the server then aggregates the model updates to get a new version of the global model (step 4).

During a single round, each client node that participates in that iteration only trains for a little while. This means that after the aggregation step (step 4), we have a model that has been trained on all the data of all participating client nodes, but only for a little while. We then have to repeat this training process over and over again to eventually arrive at a fully trained model that performs well across the data of all client nodes.

### Conclusion
Congratulations, you now understand the basics of federated learning. There’s a lot more to discuss, of course, but that was federated learning in a nutshell. In later parts of this tutorial, we will go into more detail. Interesting questions include: How can we select the best client nodes that should participate in the next round? What’s the best way to aggregate model updates? How can we handle failing client nodes (stragglers)?

### Federated evaluation
Just like we can train a model on the decentralized data of different client nodes, we can also evaluate the model on that data to receive valuable metrics. This is called federated evaluation, sometimes abbreviated as FE. In fact, federated evaluation is an integral part of most federated learning systems.

### Federated analytics
In many cases, machine learning isn’t necessary to derive value from data. Data analysis can yield valuable insights, but again, there’s often not enough data to get a clear answer. What’s the average age at which people develop a certain type of health condition? Federated analytics enables such queries over multiple client nodes. It is usually used in conjunction with other privacy-enhancing technologies like secure aggregation to prevent the server from seeing the results submitted by individual client nodes.

### Differential Privacy
Differential privacy (DP) is often mentioned in the context of Federated Learning. It is a privacy-preserving method used when analyzing and sharing statistical data, ensuring the privacy of individual participants. DP achieves this by adding statistical noise to the model updates, ensuring any individual participants’ information cannot be distinguished or re-identified. This technique can be considered an optimization that provides a quantifiable privacy protection measure.


