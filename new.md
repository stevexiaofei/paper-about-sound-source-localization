import java.io.BufferedReader;  
import java.io.BufferedWriter;  
import java.io.FileReader;  
import java.io.FileWriter;  
import java.io.IOException;  
import java.util.ArrayList;  
import org.json.JSONException;  
import org.json.JSONObject;  
import org.json.JSONArray; 

public class json_parser{  
	public static void main(String[] args) {
		try {
			BufferedReader br = new BufferedReader(new FileReader("example.json"));
			String s = br.readLine();
			JSONObject dataJson = new JSONObject(s);
			JSONArray features = dataJson.getJSONArray("features");
			for (int i = 0; i < features.length(); i++) {  
				double it = features.getDouble(i);
				System.out.println(it);		
			}  
		} catch (IOException e) {  
            // TODO Auto-generated catch block  
            e.printStackTrace();  
        }  
	}
}
https://math.nist.gov/javanumerics/jama/doc/
