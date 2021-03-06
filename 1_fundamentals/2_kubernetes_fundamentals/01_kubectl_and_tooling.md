# Lab - Getting Started with Kubernetes

## Make sure you have a Kubernetes cluster to work with. AKS or at the very least, Docker-desktop Kubernetes. 

## Exercise 1 - Interacting with Kubernetes using `kubectl`

```kubectl```, pronounced *Kube-Control*, is the  command line interface for running commands against a Kubernetes clusters. In this execise, you will explore some useful features of ```kubectl``` that you may find useful.

> **Note:** If you're using an aks cluster, make sure you've run `az aks get-credentials -n <cluster-name> -g <resource-group-name>` and retrieved the credentials

1. Get Cluster information.

    ```bash
    kubectl cluster-info
    ```

2. Get contexts (clusters you're connected to)

    ```bash
    kubectl config get-contexts
    ```
    
namespaces

Namespaces are very important objects in Kubernetes and they act like virtual clusters and allow you to isolate your workloads. Make sure to create yourself a namespace if you hadn't done so. Otherwise, all your resources will go to a namespace called `default` which is not recommended.

Example:
```bash
kubectl create ns hackathon
# set it as default
kubectl config set-context --current --namespace=hackathon
# see all ns
kubectl get ns
# see current context and its default ns
kubectl config get-contexts
```
For more, see: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/

---
3. Get a list of all namespaces in the cluster. Here you will see other participants namespaces too. In real world, you may or may not have access to view other namespaces.


    ```bash
    kubectl get namespaces

    # or use the short-hand
    kubectl get ns

    # filter your namespace by using `grep`
    kubectl get ns | grep '<your-namespace-name>' 
    ```

4. Check your `default` namespace for your current context.

    ```bash
    kubectl config get-contexts     
    ```

    You should see an output such as below with your `your namespace name` appearing under NAMESPACE column. 

    ```bash
    # example output for above command 
    CURRENT   NAME              CLUSTER             AUTHINFO               NAMESPACE
    *         docker-desktop    docker-desktop      docker-desktop         <your-namespace-name>
    ```

    > Important: If the NAMESPACE column is blank or doesn't contain your namespace name, it means you haven't configured your default namespace correctly. 

4. Get a list of nodes in the cluster.

    ```bash
    kubectl get nodes 

    # see more info with -o wide
    kubectl get nodes -o wide

    ```
 ---

## Exercise 2 - Use `Explain` for docs

```kubectl explain``` will explain the given resource. For example, a top level API-object like Pod or a specific field like a Pod's container. 

1. Get the documentation of a Pod resource and its fields.

    ```bash
    kubectl explain node
    ```

2. Get the documentation for a Pod's container specification.

    ```bash
    # to just explain pod
    kubectl explain pod  

    # to further explain the properties and nested objects
    kubectl explain pods.spec.containers
    ```

    > **Note:** ```pods.spec.containers``` matches the yaml object structure:
    > ```yaml
    > apiVersion: v1
    > kind: Pod
    > metadata:
    > creationTimestamp: null
    > name: nginx
    > spec:
    >   containers:
    >   - image: nginx
    >     imagePullPolicy: IfNotPresent
    >     name: nginx
    >     resources: {}
    >   dnsPolicy: ClusterFirst
    >   restartPolicy: Never
    > ```

---

## Bonus: (skip it for now if you're a bit behind on the schedule) Exercise 3 - Retrieve CPU and Memory metrics from your nodes

1. See cpu and memory usage of all nodes

    ```bash        
    kubectl top nodes    
    ```

    * This may not work out of the box on Docker-desktop k8s if Metrics Server is not installed.
    * If possible, troubleshoot this by doing a quick search on how to enable metrics-server for Docker-desktop k8s. 
    * For hints, also take a look at [metrics-server dir](./metrics-server/) under `bonus`
    
        > Or just move on to next step for now.

    If you're interested to know more about metrics in k8s, take a look at:   

    * [Metrics server in k8s](https://github.com/kubernetes-sigs/metrics-server)
    
    * [Cpu units in K8s](https://kubernetes.io/docs/tasks/configure-pod-container/assign-cpu-resource/#cpu-units)

    * [Memory units in K8s](https://kubernetes.io/docs/tasks/configure-pod-container/assign-memory-resource/#memory-units)

2. Find out `CPU` and `memory` limits on the first node in cluster

    The resource limits you see here were created by existing workloads such as dashboard, metrics-server, etc

    ```bash    
    # get list of nodes
    kubectl get nodes     
    # describe a particular node
    kubectl describe node <name-of-first-node> 
    ```

    **or**
    ```bash
    # once you become more familar with jsonpath, you could do the same like this
    kubectl get nodes -o jsonpath='{.items[0].metadata.name}' | kubectl describe node
    ```

    **or if you prefer to use tools like `jq`**
    ```bash
    kubectl get nodes -o json | jq '.items[0].metadata.name' | kubectl describe node
    ```
---

## Bonus

1. Take a quick look at `kuberenetes` cheatsheet. It's useful and will come in handy when diving deeper into `kubectl`.

    * [Kubernetes Cheatsheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)

 > **_Skip below two tasks for now if you are new to bash / linux command line_**

2. Setup an alias for `kubectl`

    * In `bash`, you can setup aliases, so that you don't have to type `kubectl` everytime and just use the shorthand.

        * For e.g. it's common to configure `k` for `kubectl`

    * This can be done by adding `alias k=kubectl` to your `.bashrc` file in your home directory.

        ```bash    
        # open .bashrc from your home directory using your editor of choice. 
        code ~/.bashrc

        # Go to end of the file and add the alias
        alias k=kubectl

        # Save and exit the editor
        # ctrl + s or file menu -> save
        ```  

    * Restart the shell or run a `source` from the terminal.

        ```bash
        source ~/.bashrc
        ```

2. Enable kubectl `autocompletion` for bash (or zsh)   

    Enable auto completion for your kubectl to improve the CLI experience. 

    https://kubernetes.io/docs/tasks/tools/install-kubectl/#optional-kubectl-configurations

