*************************************************************************************************************
Project: TileNET: Scalable Architecture for High-throughput Ternary Convolution Neural Networks using FPGAs

This file contains the detailed explanation for the performance estimator for TileNET.

*************************************************************************************************************

1. The organization of the files is as follows:
performance_estimator
-streaming
 -Alexnet_devices.xlsx
 -lenet_devices.xlsx
 -resnet50_devices.xlsx
 -vgg16_devices.xlsx
-systolic
 -Alexnet_devices_sys.xlsx
 -lenet_devices_sys.xlsx
 -resnet50_devices_sys.xlsx
 -vgg16_devices_sys.xlsx

2A. Input to the estimator model are
-Device(FPGA Device: Zync,Virtex,Artix,Kintex)
-Convolution Neural Network Architecture(AlexNet, VGG16,Resnet50,LeNet)
-Implementation results of a PE on target device

2B.Outputs of the estimator are
-%utilization of memory(BRAMs)
-%utilization of compute(LUTs)
-Performance(TOPS)

3.Select architecture type : Streaming or Systolic(top folders)

4.Select CNN architecutre: AlexNet, VGG16,Resnet50,LeNet(excel sheets)

5.Select FPGA device : Zync,Virtex,Artix,Kintex(excel sheet tabs)

6.Input as shown into following sections:
-Implementation results of a PE on target device
-Target device specific resources 
-Frequency of operation

e.g.:
Frequency	500000000

DATA GIVEN	LUT	Virtex	
PE	        176	#slices	        178000
		        #brams(36 kb)	1880

7.Output
-%utilization of memory(BRAMs)
-%utilization of compute(LUTs)
-Performance(TOPS)
TOPs	        13.76263066
%lut-l util	92.66516854
%lut-m util	29.89361702

8. Adjust Parallelization factor P and F to get the desired results

9. Calculations: 
9A.Throughput
#Ternary Compute= M*N*3*3*(#times_kernel)/(P*F)

Performance in TOPS= 2*Total_Ternary_Compute*FrequencyofOperation/1000000000000
Where
#Ternary Compute :number of ternary computation
#times kernel: when kernel size>3x3,number of times 3x3 has to be repeated to accommodate the larger kernel size
P,F: Parallelization factors in Output and Input Depth


9B.  Memory:
(i)Input BRAM=0.5*kernel*CEILING.MATH(N/4) 
// comment:kernel because we want kernel number of rows( can be increased to kernel+1)
//N/4: because the BRAM can hold 32 bits each, we require only 8 bits for each vector: hence 32/8=4
//0.5: because BRAM is 36Kb and we need to use only 18Kb for 3X3 kernel sizes

(ii)Kernel BRAM=0.5*(CEILING.MATH((kernel*kernel*2)/72))*N*M/(P*F)
//comment : 0.5 is multiplied when kernel is 3x3: can be accommodated on half of BRAM(36Kb)

9C. Logic
#LUTs= (#TernaryCompute * PE_LUT)/9
#LUTs: Number of lookup tables
PE_LUT: LUTs used in implementation of PE(9 ternary computes) on target device

and then % utilization for each is computed as sum of all BRAMs required in streaming mode or maximum of all resources requird in systoloc mode
