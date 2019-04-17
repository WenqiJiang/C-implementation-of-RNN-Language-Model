# C-implementation-of-RNN

## To be done
git pull rnn and update keras_rnn_lm folder

## Introduction
This is the first step to implement RNN language models on FPGAs. All modules are heavily commented. We will use High-Level Synthesis to turn these code into Hardware Description Languages (HDL). 

## Run Program
We prepared everything for you, simply run 'make' in command line, then './main' to see the result generated by 1000 samples.

If you don't want to run the program by yourself, there are some sample results:

log_double.out: 27.8125% 

log_float.out: 26.8750% 

These results are the inference accuracy of the next words. Each word is predicted by input the sequences of previous 50 words to RNN. We used 'double' and 'float' in C to test the results, which are float64 and float32. However, these results are slightly less accurate than Keras, which has an accuracy of 28.1250%. 

## Module Description

### RNN computations
fc.c: Fully-connected Layers 

rnn.c: RNN Layers 

### Activation functions
activation.c: relu & tanh 

softmax.c: softmax 

### Run program
main.c: float32 inference 

main_double.c: float64 inference 


### Dataset & weights
./datasets: sequences composed of 50 words, the result we want to predict is the next single word 

./h5: convert the original one HDF5 file which contains weights to several txt files 

./model: all weights in txt version, with high precision (at least for float and double) 


### Others
constant.h: RNN dimensions, word dictionary size, etc. 

load_data.c: load weights and datasets from txt files to C 

params_init.c: initialize weights to zeros (usually used with malloc) 

## Accuracy Analysis against Keras 
The accuracy of C implemetation results is slightly worse than Keras. We proposed several reasons and tried to fix them. However, after excluded several possible reasons, there are still something that we can not implemented in C, which may be the cause of accuracy drop.

### Tanh in C is not accurate 

	c standard lib manual: use our tanh instead of build yours 

### H5dump to txt is not precise enough

	I write some python to output very precise txt format float numbers. But result is still the same. 

### Float / double precision

	double -> very close to Keras result (0.3% accuracy drop) while 
  
	float -> 1.3% drop 

### Softmax is not accurate

	can influence result probability distribution accuracy, but not result 

### Keras has some internal optimization layers, such as batch norm 

	not very possible, at least I didn't explicitly write that 

### Keras has optimization of matrix multiplication accuracy 
	possible, but I have no idea how to implement that in C 

### Conclusion

  Most possible reasons:
  
	(1) Keras optimization for tanh
  
	(2) Keras matrix multiplication optimization (on precision)
	
	both are not easy to implement in our C code
## H5dump

list all contents: 
h5dump -n pre-trained-rnn.h5 

open an attribute 
h5dump -a "/model_weights/dense_1/weight_names" pre-trained-rnn.h5 

view a dataset (show weights) 
h5dump -d "/model_weights/dense_1/dense_1/bias:0" pre-trained-rnn.h5 

h5 to txt: 
h5dump -o dense_1_bias.txt -y -w 1000000000 dense_1_bias.h5 
