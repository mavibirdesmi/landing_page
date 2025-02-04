---
title: Database Optimization
weight: 3
---

## Database Optimization Strategies

### How do I reduce memory usage?

The primary source of memory usage vector data. There are several ways to address that:

- Configure [Quantization](../../guides/quantization/) to reduce the memory usage of vectors.
- Configure on-disk vector storage

The choice of the approach depends on your requirements. 
Read more about [configuring the optimal](../../tutorials/optimize/) use of Qdrant. 

### How do you choose machine configuration?

There are two main scenarios of Qdrant usage in terms of resource consumption:

- **Performance-optimized** - when you need to serve vector search as fast(many) as possible. In this case, you need to have as much vector data in RAM as possible. Use our [calculator](https://cloud.qdrant.io/calculator) to estimate the required RAM.
- **Storage-optimized** - when you need to store many vectors and minimize costs by compromising some search speed. In this case, pay attention to the disk speed instead. More about it in the article about [Memory Consumption](../../../articles/memory-consumption/).

### I configured on-disk vector storage, but memory usage is still high. Why? 

First of all, memory usage metrics as reported by `top` or `htop` might be misleading. They are not showing the amount of memory required to run the service.
If you see RSS memory usage of 10GB, it doesn't mean that it won't work on a machine with 8GB of RAM.

Qdrant uses many techniques to reduce search latency, including caching the disk data in RAM and preloading the data from disk to RAM.
As a result, the Qdrant process might use more memory than the minimum required to run the service.

If you want to limit the memory usage of the service, we recommend using [limits in docker](https://docs.docker.com/config/containers/resource_constraints/#memory) or Kubernetes.


### My requests are very slow or time out. What should I do?

There are several possible reasons for that:

- **Using filters without payload index** - If you're performing a search with a filter but you don't have a payload index, Qdrant will have to load whole payload data from disk to check the filtering condition. Ensure you have adequately configured [payload indexes](../../concepts/indexing/#payload-index).
- **Usage of on-disk vector storage with slow disks** - If you're using on-disk vector storage, ensure you have fast enough disks. We recommend using local SSDs with at least 50k IOPS. Read more about the influence of the disk speed on the search latency in the article about [Memory Consumption](../../../articles/memory-consumption/).
- **Large limit or nonoptimal query parameters** - A large limit or offset might lead to significant performance degradation. Please pay close attention to the query/collection parameters that significantly diverge from the defaults. They might be the reason for the performance issues.
