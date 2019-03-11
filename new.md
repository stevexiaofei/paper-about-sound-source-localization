# 工作日记
Face Recognition Based onFitting a 3D Morphable Model
import math
import numpy as np 
import tensorflow as tf

from tensorflow.python.framework import ops

from utils import *
def convolutionID(input,filter_length,output_channel,name='conv1d'):
	'''
		@param:
			input:assume input shape is (None,1,num_features,channels)
	'''
	half_length=int(filter_length/2)
	inputs = tf.concat([input[:,:,-half_length:,:],input,input[:,:,:half_length,:]],2)
	initializer=tf.contrib.layers.xavier_initializer(uniform=False)
	kernel = tf.get_variable('weights',[1,filter_length, input.get_shape().as_list()[-1], output_channel],initializer=initializer )
	conv = tf.nn.conv2d(inputs, kernel, [1,1,1,1], padding='VALID', data_format='NHWC')
	norm = tf.contrib.layers.batch_norm(conv, 0.9, epsilon=1e-5, activation_fn = tf.nn.relu,updates_collections=None,scale=True)
	return norm
def deconv1d(x, W,stride):
	x_shape = tf.shape(x)
	w_shape = W.get_shape()
	output_shape = tf.stack([x_shape[0], x_shape[1], x_shape[2]*2, w_shape[2]])
	return tf.nn.conv2d_transpose(x, W, output_shape, strides=[1, 1, stride, 1], padding='VALID')
def upsample(input,pool_size,output_channel):
	initializer=tf.contrib.layers.xavier_initializer(uniform=False)
	kernel = tf.get_variable('weights',[1,pool_size, output_channel, input.get_shape().as_list()[3]],initializer=initializer)
	h_deconv = tf.nn.relu(deconv2d(inputs, kernel, pool_size))
	return h_deconv
	
