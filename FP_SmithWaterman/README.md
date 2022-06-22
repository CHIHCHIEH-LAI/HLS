# A Scalable Smith-Waterman Algorithm Accelerator

Propose a scalable design of [Smith-Waterman algorithm](https://en.wikipedia.org/wiki/Smith%E2%80%93Waterman_algorithm) accelerating on U50 fpga with [Vitis](https://www.xilinx.com/products/design-tools/vitis/vitis-platform.html). 

## Introduction

### Smith-Waterman Algorithm
The Smith-Waterman algorithm performs local sequence alignment, that is, for determining similar regions between two strings of DNA or RNA sequences. Instead of looking at the entire sequence, the Smith-Waterman algorithm compares segments of all possible lengths and optimized the similarity measure. For more information about smith-waterman, please refer to [docs](https://github.com/CHIHCHIEH-LAI/HLS/blob/main/FP_SmithWaterman/docs) or [wiki](https://en.wikipedia.org/wiki/Smith%E2%80%93Waterman_algorithm).

### Project Aim
This project accelerates the Smith-Waterman algorithm on U50 fpga with Vitis and aims to make the design scalable for database string size. The system is composed of one software host application (maincl.cpp) and one hardware kernel component (compute_matrices.cpp). The host application ramdonly generates a query and a database sequence and send the two sequences to hardware kernel to calculate direction maxtrix and index of max value. And then the host testifies whether the kernel function results are right or not. The hardware-accelerated kernel implements the Smith-Waterman algorithm on FPGA. We place particular emphasis on the ability to process database string of any lengths effectively, as this would be required to make the design more general and scalable.

## Major Optimizations

### Highlighted Areas of Optimization

#### Find Index of Max Value
![iamge](https://github.com/CHIHCHIEH-LAI/HLS/blob/main/FP_SmithWaterman/imgs/backtracking.jpg)
When doing backtracking, the host first needs to start from the point with max value and then trace back according to direction matrix. However, the [original design of Smith-Waterman](https://github.com/CHIHCHIEH-LAI/HLS/tree/main/FP_SmithWaterman/src/original) does not calculate max value index

### Results

## Rebuild the Project

### Requirements

* Ubuntu 18.04, or other GNU/Linux distributions
* Docker without root
* Vivado 2020.1 and Vivado 2020.2
* [PyTorch >= 1.5.0](https://pytorch.org/)
* [Brevitas >= v0.7.0](https://github.com/Xilinx/brevitas)
* [FINN v0.7](https://github.com/Xilinx/finn/releases/tag/v0.7)
* [TUL PYNQ-Z2 development board](https://www.tulembedded.com/FPGA/ProductsPYNQ-Z2.html)

### Procedure

1. Install Brevitas and replace `brevitas_examples/bnn_pynq/` with `./bnn_pynq/` .

   Follow the instruction in [`./bnn_pynq/README.md`](./bnn_pynq/README.md) to train the `VGG-5` model, \
   and then move the trained `./bnn_pynq/VGG.onnx` to `./FINN/model.onnx` .

2. After setting up environment variables for FINN, execute

   ```sh
   ./run-docker.sh build_custom Xilinx-HLS/Preprocess_CNN_Pipeline/FINN
   ```

   under the root directory of FINN to build the CNN IP in advanced build mode.

3. The deployment package will be generated under \
   `./FINN/output_vgg_gray_Pynq-Z2/deploy/` .

   If batched top-1 accuracy validation is needed, put \
   `./FINN/driver/validate.py`, `./FINN/driver/testx_gray.npy`, `./FINN/driver/testy.npy` \
   into the deployment package.

4. The out-of-context stitched IP will be generated under \
   `./FINN/output_vgg_gray_Pynq-Z2/stitched_ip/ip/` .

   It can be further used in building customized Composable Pipeline.
