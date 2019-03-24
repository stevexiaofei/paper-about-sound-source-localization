import java.util.*;
import org.apache.commons.math3.complex.Complex;
import org.apache.commons.math3.transform.DftNormalization;
import org.apache.commons.math3.transform.FastFourierTransformer;
import org.apache.commons.math3.transform.TransformType;

interface TestCase{
   public Object run(List<Object> params) throws Exception;
   public List<Object> getParams();
}
class CalcFFT implements TestCase{
	public CalcFFT(){
      System.out.print("本算例用于计算快速傅立叶变换。正在初始化 计算数据(" + arrayLength + "点)... ...");
      inputData = new double[arrayLength];
      for (int index = 0; index < inputData.length; index++){
         inputData[index] = (Math.random() - 0.5) * 100.0;
      }
      System.out.println("初始化完成");
   }
   @Override
   public List<Object> getParams(){
      return null;
   }
   @Override
   public Object run(List<Object> params) throws Exception{
      FastFourierTransformer fft = new FastFourierTransformer(DftNormalization.STANDARD);
      Complex[] result = fft.transform(inputData, TransformType.FORWARD);
      return result;
   }
   private double[] inputData = null;
   private final int arrayLength = 1024;
}

public class TimeCostCalculator{
   public TimeCostCalculator()
   {
   }
   /**

    * 计算指定对象的运行时间开销。

    * 

    * @param testCase 指定被测对象。

    * @return 返回sub.run的时间开销，单位为s。

    * @throws Exception

    */

   public double calcTimeCost(TestCase testCase) throws Exception{
      List<Object> params = testCase.getParams();
      long startTime = System.nanoTime();
      testCase.run(params);
      long stopTime = System.nanoTime();
      System.out.println("start: " + startTime + " / stop: " + stopTime);
      double timeCost = (stopTime - startTime) * 1.0e-9;
      //      double timeCost = BigDecimal.valueOf(stopTime - startTime, 9).doubleValue();

      return timeCost;
   }
   public static double[] data_processing(double[] input){
		int data_len = input.length;
		int seg_len = data_len/3;
		double[] res = new double[data_len/4];
		FastFourierTransformer fft = new FastFourierTransformer(DftNormalization.STANDARD);
		for(int i=0;i<data_len;i++){
			System.out.println(input[i]);
		}
		int index=0;
		for(int i=0;i<3;i++){
			double[] temp =new double[data_len/6];
			System.out.println(' ');
			for(int j=0;j<temp.length;j++){
				temp[j] = input[i*seg_len+2*j];
				System.out.println(temp[j]);
			}
			System.out.println(' ');
			Complex[] result = fft.transform(temp, TransformType.FORWARD);
			for(int j=0;j<result.length/2;j++){
				res[index++]=result[j].abs();
			}
			
		}
		
		//Complex[] result = fft.transform(inputData, TransformType.FORWARD);
	   return res;
	   
   }
   public static void main(String[] args) throws Exception{
	   double[] input =new double[24];
	   for(int i=0;i<input.length;i++){
		   input[i]=i;
	   }
	   double[] res = data_processing(input);
	   for(int i=0;i<res.length;i++){
		   double it =res[i];
		   System.out.println(it);
	   }
      // TimeCostCalculator tcc = new TimeCostCalculator();
      // double timeCost;
      // System.out.println("--------------------------------------------------------------------------");
      // timeCost = tcc.calcTimeCost(new CalcFFT());
      // System.out.println("time cost is: " + timeCost + "s");
      // System.out.println("--------------------------------------------------------------------------");
   }
   
}
