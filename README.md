# K8s v1.28 Sidecar container RestartPolocy

A new feature has been introduced to kuber since version 1.28 (still alpha)

## What is a Sidecar Container?  

A sidecar container is a design pattern that allows you to run an additional container alongside your main container in the same pod. The sidecar container can perform tasks that complement the main container, such as syncing data from a remote source, collecting and shipping logs, providing health checks and metrics, proxying network traffic, encrypting or decrypting data, or injecting faults for testing.  

The sidecar container shares the same lifecycle, resources, and network namespace as the main container but has its own file system and process space. This means that the sidecar container can access the same ports, volumes, and environment variables as the main container but cannot interfere with its execution.  

## Sidecar container RestartPolocy Example

Kubernetes 1.28 adds a new restartPolicy field to init containers that is available when the SidecarContainers feature gate is enabled.  

let's look at the example below:  

        apiVersion: v1
        kind: Pod
        metadata: 
          name: myapplication-pod
          labels: 
            app: myapplication
        spec:
          restartPolicy: OnFailure
          initContainers:
          - name: network-proxy
            image: network-proxy:1.0
            restartPolicy: Always
          containers:
          ...


In this config the sidecar container will restart if it crashes. it will run as long as the main container is running and it will be terminated when the main container terminates. 

## Why we need this

The built-in sidecarfeature solves for the use case of having a lifetime equal to the Pod lifetime and has the following additional benefits:  

- Provides control over startup order
- Doesn’t block Pod termination

## When to use sidecar containers

You might find built-in sidecar containers useful for workloads such as the following:

- Batch or AI/ML workloads, or other Pods that run to completion. These workloads will experience the most significant benefits.
- Network proxies that start up before any other container in the manifest. Every other container that runs can use the proxy container's services. For instructions, see the Kubernetes Native sidecars in Istio blog post.
- Log collection containers, which can now start before any other container and run until the Pod terminates. This improves the reliability of log collection in your Pods.
- Jobs, which can use sidecars for any purpose without Job completion being blocked by the running sidecar. No additional configuration is required to ensure this behavior.


