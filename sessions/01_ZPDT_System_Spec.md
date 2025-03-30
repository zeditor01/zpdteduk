# ZPDT System Spec

## Choice of Intel System

the more you spend, the better the experience.

### Minimum Viable System - IBM provided laptop.

Get IBM to provide a reasonabily modern laptop.
1. CPU is not as critical as memory or SSD, but the faster the better. Laptop CPUs tend to have fewer cores and lower clock speed than systems that you can build yourself, because of cooling challenges in the laptop form factor.
2. 32GB memory is the bare minimum to run Linux on x86, the ZPDT emulator, and the guest z/OS system. 
3. 1TB SSD is the bare minumum, but that will not allow youto have two z/OS systems ( Demo system + New Build )

### Budget System - an Intel NUC.

Intel NUC system are great as headless servers, where you use RDP and 3270 emulators to connect in.
1. Intel created the NUC concept, and provide robust cost-effective models.
2. Suggest a modern Corei3 CPU with multiple cores. 
3. 32GB memory is OK. Current NUC motherboards often support 64GB, which is a low cost but effectove upgrade option. 
4. If you're buildng a NUC, a 2TB NVMe SSD is another wide upgrade because it will not allow youto have two z/OS systems ( Demo system + New Build )

### Lavish System

A micro-ATX moptherboard and case will allow you to install high end CPUs with decent CPU coolers to get greater performance.
neale built the following system in 2024.

```
AMD Ryzen 9 7900X 4.7 GHz 12-Core Processor 	$610.11 	
Deepcool LD240 72.04 CFM Liquid CPU Cooler 	$145.00 	
MSI PRO B650M-P Micro ATX AM5 Motherboard 	$165.00 	
Kingston FURY 128 GB (4 x 32 GB) DDR5-5600 CL40 Memory 	$605.20 	
Samsung 990 Pro 4 TB M.2-2280 PCIe 4.0 X4 NVME Solid State Drive 	$529.00 	
Asus GT1030-2G-CSM GeForce GT 1030 2 GB Video Card 	$277.00 	
Deepcool CH360 DIGITAL MicroATX Mid Tower Case 	$99.00 	
Corsair RM750e (2023) 750 W 80+ Gold Certified Fully Modular ATX Power Supply 	$144.00 	
Total: 	$2574.31
```
