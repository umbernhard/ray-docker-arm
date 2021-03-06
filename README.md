Fork of the [Ray Dockerfile directory](https://github.com/ray-project/ray/tree/master/docker). Replaces images for x86_64 systems with aarch64.

Overview of how the ray images are built:

Images without a "-cpu" or "-gpu" tag are built on ``ubuntu/focal``. They are just an alias for **-cpu** (e.g. ``ray:latest`` is the same as ``ray:latest-cpu``).

```
ubuntu/focal
└── base-deps:cpu
    └── ray-deps:cpu
        └── ray:cpu
            └── ray-ml:cpu

nvidia/cuda
└── base-deps:gpu
    └── ray-deps:gpu
        └── ray:gpu
            └── ray-ml:gpu
```
