import java.io.BufferedReader;  
import java.io.BufferedWriter;  
import java.io.FileReader;  
import java.io.FileWriter;  
import java.io.IOException;  
import java.util.ArrayList;  
import org.json.JSONException;  
import org.json.JSONObject;  
import org.json.JSONArray; 
import Jama.Matrix; 

class mlp_model{  
	private Matrix w1;
	private Matrix b1;
	private Matrix w2;
	private Matrix b2;
	private Matrix test_x;
	public mlp_model(String weight_path){
		try {
			BufferedReader br = new BufferedReader(new FileReader(weight_path));
			String s = br.readLine();//while ((s = br.readLine()) != null) 
			JSONObject dataJson = new JSONObject(s);
			JSONArray w1 = dataJson.getJSONArray("w1");
			this.w1 = parser_mat(w1,120,10);
			JSONArray b1 = dataJson.getJSONArray("b1");
			this.b1 = parser_mat(b1,1,10);
			JSONArray w2 = dataJson.getJSONArray("w2");
			this.w2 = parser_mat(w2,10,2);
			JSONArray b2 = dataJson.getJSONArray("b2");
			this.b2 = parser_mat(b2,1,2);
			JSONArray x = dataJson.getJSONArray("x");
			this.test_x = parser_mat(x,1,120);
		} catch (IOException e) {  
            // TODO Auto-generated catch block  
            e.printStackTrace();  
        }  
	}
	public int predict(Matrix x){
		//Matrix input = new Matrix(x,x.length);
		Matrix activate_1 =this.b1.plus(x.times(this.w1));
		int col=activate_1.getColumnDimension();
		for(int i=0;i<col;i++){
			double tmp=activate_1.get(0,i);
			if(tmp<0)
				tmp=0;
			activate_1.set(0,i,tmp);
		}
		Matrix out = this.b2.plus(activate_1.times(this.w2));
		//out.print(2,2);
		return out.get(0,0)>out.get(0,1)? 0:1;
	}
	public void model_test(){
		predict(this.test_x);
	}
	private static Matrix parser_mat(JSONArray weight,int m,int n){
		Matrix W = new Matrix(m,n);
		for (int i = 0; i < weight.length(); i++) {  
				double it = weight.getDouble(i);
				int row = i/n,col=i%n;
				W.set(row,col,it);
		}  
		//System.out.println(W.getRowDimension());
		//System.out.println(W.getColumnDimension());
		//W.print(2,2);
		return W;
	}
}
public class json_parser{
	
	public static void main(String[] args) {
		mlp_model model=new mlp_model("params.json");
		model.model_test();
	}
}
