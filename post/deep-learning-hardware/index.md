---
title: "Deep Learning Server Budget Build"
tags: [
    "DeepLearning",
    "Hardware"
]
image: "https://picsum.photos/1024/400/?image=1075"
date: "2019-01-01"
author: Steven
description: Building a DL server on budget
---


I was looking for a budget build workstation for deep learning model training. 
I decided that the system to start with should have the ***best price-to-flop performance***, since my experience with the latest Titan V/Telsa V100 on Google Cloud is not very good. Training complex models are not significantly faster than the TitanXP in our lab. 
It will still take some time for the major deep learning frameworks to fully exploit the advantages of the added Tensor Cores.
With the same budget, I can already get 4x1080ti which can significantly boost my training speed by data parallelization. 
Belows are benchmarks and specs from [Yusaku Sako](https://github.com/u39kun/deep-learning-benchmark). My biased observation is that, *optimizing my code will make my models get trained faster than switching to the latest GPU for my use cases*.

<style>
td {
        border-bottom: 1px groove #c7c8cb;
        border-top: 1px groove #c7c8cb;
        padding-left:20px;
}

table{
    background-color: rgba(0, 0, 0, .486);
    padding: 20px;
    font-size: 10pt;
    width: 100%;
}

img{
    background-color: rgba(0, 0, 0, .486);
    width:100%;
}

center{
    overflow-x: auto;
}
</style>



<center> 

|Model     |Architecture|Memory    |CUDA |Tensor Cores|F32 TFLOPS|F16 TFLOPS|Cost|
|----------|------------|----------|----------|------------|----------|----------|------|
|Tesla V100|Volta       |16GB HBM2 |5120      |640         |15.7      |125       | $3.06/hr|
|Titan V   |Volta       |12GB HBM2 |5120      |640         |15        |110*      |$2999  |
|1080 Ti   |Pascal      |11GB GDDR5|3584      |0           |11        |N/A       |$699  |        

</center>

The benchmark below is taken from [Lambda Lab Benchmark](https://lambdalabs.com/blog/best-gpu-tensorflow-2080-ti-vs-v100-vs-titan-v-vs-1080-ti-benchmark/). Values represent the speed-up-against-1080ti/$1000 (F32). Again, for my use case this number should be much lower, since the GPU is not very well utilized. 

<center> 

Model/GPU	|	2080 Ti	|	Titan V	|	V100	|	1080 Ti
----|----|----|----|----
Price Per GPU (k$)	|	1.2	|	3	|	9.8	|	0.7
Price Per 1 GPU System (k$)	|	2.49	|	4.29	|	11.09	|	1.99
AVG	|	0.55	|	0.33	|	0.16	|	0.5
ResNet-50	|	0.56	|	0.34	|	0.16	|	0.5
ResNet-152	|	0.53	|	0.31	|	0.14	|	0.5
InceptionV3	|	0.58	|	0.37	|	0.17	|	0.5
InceptionV4	|	0.57	|	0.32	|	0.14	|	0.5
VGG16	|	0.51	|	0.33	|	0.16	|	0.5
AlexNet	|	0.52	|	0.32	|	0.16	|	0.5
SSD300	|	0.55	|	0.33	|	0.16	|	0.5

</center>

Then I came across the PC build of the [Lambda Lab build](https://pcpartpicker.com/list/mXFJd6) and a customized build by [kylemcdonald](https://ca.pcpartpicker.com/b/b9KBD3) on [pcpartpicker.com](pcpartpicker.com). I combine them as below:

 | | 
----|-----|----
CPU	|	Intel - Core i7-6850K 3.6 GHz 6-Core Processor	|	$863.99
CPU Cooler	|	Intel - BXTS13A CPU Cooler	|	$21
Motherboard	|	Asus - X99-E WS/USB 3.1 SSI CEB LGA2011-3 Motherboard	|	$680.84
Memory	|	Corsair - Vengeance LPX 64 GB (4 x 16 GB) DDR4-2666 Memory	|	$612
Storage	|	Crucial - MX300 1.05 TB 2.5" Solid State Drive	|	$341.99
Video Card	|	PNY 1080 T​i Founders​ Edition (or any blower-style 1080ti)	|	
	|	PNY 1080 T​i Founders​ Edition (or any blower-style 1080ti)	|	
	|	PNY 1080 T​i Founders​ Edition (or any blower-style 1080ti)	|	
	|	PNY 1080 T​i Founders​ Edition (or any blower-style 1080ti)	|	
Case	|	Corsair - Carbide 400Q ATX Mid Tower Case	|	$164.74
Case	|	Carbide Se​ries® Air ​540 Arctic​ Black	|	$169.99
Power Supply	|	EVGA - SuperNOVA P2 1600 W 80+ Platinum Certified Fully-Modular ATX Power Supply	|	$347
Total |		|	$3,201.55

The total price above does not include the GPUs. The price of 1080ti varies depending on where you want to get one.
I will probably get those from e-Bay, so the cost can be significantly lower. 
Based on the reviews, GPUs from e-Bay work fairly well for reasonable price. 
Of course one needs to be cautious with those offers that are too good to be true.  

Any 1080ti version that comes with a blower style thermal solution should be compatible:

{{< figure src="./1080ti.png" alt="image" caption="Asus GTX 1080ti Turbo" >}}

The above setup is versatile, it can fit other NV-brand GPUs. 
So I can upgrade my GPU later on.
That's why I still use brand-new parts for the other components. 

It is noted that the CPU supports 40 PCIe lanes. Even though there will require 4*16 lanes for four GPUs,
the motherboard comes with a PLX switch that can simulate 16 lanes for each GPU.
So one can avoid the wired ```16x8x8x8``` setups.
However, ***the maximum bandwidth still limits to 40 lanes at the same moment***.
This would be problematic for data parallelization, since we need to wait for the slowest GPU to finish before moving on the next step.
This problem cannot be fixed without a budget < $20,000 (we need two CPUs).
For now I will just ignore this issue. 

