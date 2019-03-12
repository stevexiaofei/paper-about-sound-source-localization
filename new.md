# 工作日记

			Args:
				sess: TensorFlow session
		"""
		self.sess = sess
		self.cfg= cfg
		self.num_mics = cfg['num_mics']
		self.input_channels = cfg['num_channels']
		self.num_frames= cfg['num_frames']
		self.build_model()
		t_vars = tf.trainable_variables()
		restore_var = list(filter(lambda x: 'discriminator' not in x.name, t_vars))
		
		#variables_to_restore = slim.get_variables_to_restore(exclude=exclude)
		self.model_path = os.path.join(cfg['exp_name'],'checkpoint')
		self.saver = tf.train.Saver(restore_var)
		self.saver_all= tf.train.Saver()
		init_op = tf.global_variables_initializer()
		self.sess.run(init_op)
	def build_model(self):
		self.input = tf.placeholder(tf.float32,[None,self.num_frames,self.num_mics, self.input_channels],
                                        name='input')
		input = tf.reshape(self.input,[-1,self.num_mics,self.input_channels])
		out = convolutionID(input, 3, self.input_channels//2)
		out = upsample(out, 2, self.input_channels//4)#num_directions=6*2=12 channels=256
		
		out = convolutionID(out, 3, self.input_channels//8)
		out = upsample(out, 2, self.input_channels//16)#num_directions=6*4=24 channel=64
		
		out = convolutionID(out, 3, self.input_channels//32)
		out = upsample(out, 2, self.input_channels//64)#num_directions=6*8=48 channels=16
		
		out = convolutionID(out, 3, self.input_channels//128)
		out = upsample(out, 2, self.input_channels//256)#num_directions=6*16=96  channels=4
		
		out = convolutionID(out, 3, self.input_channels//512)
		out = upsample(out, 2, self.input_channels//1024)#num_directions=6*32=192 channels=1
	
