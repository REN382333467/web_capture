# web_capture
用来获取网页上的基本信息（获取天气情况）

import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;
import java.util.ArrayList;


public class web_capture {

	public static void main(String[] args) throws Exception {
		// TODO 自动生成的方法存根
		int year = 2016; //输入年
		int month = 10;     //输入月份
		int day = 0;
		if (month==1 || month==3 || month==5 || month==7 || month==8 || month==10 || month==12){
			day = 32;
		}else{
			day = 31;
		}
		if (month == 2){    //2月要单独判断
			if (((year%9 ==0)&(year%100 !=0))||(year%400 ==0)){
				day = 30;
			}
			else{
				day = 29;
			}
		}
		String y = ""+ year;
		String m = ""+ month;
		for(int i=1;i<day;i++){
			String s = ""+i;
			captureHtml(y,m,s);
		}
		System.out.println("over!!!");
		//captureHtml(year,month,"26");
	}
	public static void captureHtml(String year,String month,String day) throws Exception {  
	    String strURL = "https://www.wunderground.com/history/airport/KOQT/"+ year +"/"+ month +"/" + day +"/DailyHistory.html?req_city=Oak%20Ridge&req_statename=Tennessee&MR=1";  
	    URL url = new URL(strURL);  
	    HttpURLConnection httpConn = (HttpURLConnection) url.openConnection();  
	    InputStreamReader input = new InputStreamReader(httpConn  
	            .getInputStream(), "utf-8");  
	    BufferedReader bufReader = new BufferedReader(input);  
	    String line = "";  
	    StringBuilder contentBuf = new StringBuilder();  
	    while ((line = bufReader.readLine()) != null) {  
	        contentBuf.append(line);  
	    }  
	    String buf = contentBuf.toString();
	    int dataIndex = buf.indexOf("<h2 class=\"history-date\">");     //获取日期的索引位置
	    ArrayList<String> hours_1 = new ArrayList<String>();        //数组用于存放小时
	    ArrayList<String> weather_1 = new ArrayList<String>();        //数组用于存放小时
	    String date = buf.substring(dataIndex+25, buf.indexOf("</h2>	<div class=\"previous-link\">"));
	    //System.out.println("日期： " + date);       //显示日期
	    int make_initial = 1;    //表示是否为一天的起始
	    int make_end = 1;   //表示是否为一天的结束	    
    	int weatherBeginIndex = 0;
	    int weatherEndIndex = 0;
	    int hourBeginIndex = 0;
	    int hourEndIndex = 0;
	    
	    
	    while(buf.indexOf("<tr class=\"no-metars\">") >= 0){ 
	    	int beginIndex = buf.indexOf("<tr class=\"no-metars\">");    //获取子串的起始位置    
	    	buf = buf.substring(beginIndex-37, buf.length());     //切割子串
	    	if (make_initial  == 1){
	    		make_initial = 0;
	    	}else{
	    		weatherBeginIndex = buf.indexOf("<td >");
	    		weatherEndIndex = buf.indexOf("</td>		</tr>");
	    		weather_1.add(buf.substring(weatherBeginIndex+5,weatherEndIndex));
	    		buf = buf.substring(weatherEndIndex+5, buf.length());     //重新定义子串
	    	}
	    	hourBeginIndex = buf.indexOf("<td >");
	    	hourEndIndex = buf.indexOf("</td>");
	    	hours_1.add(buf.substring(hourBeginIndex+5,hourEndIndex));   //截取小时
	    	buf = buf.substring(hourEndIndex+5, buf.length());     //重新定义子串
	    }
	    //获取最后一个
	    buf = buf.substring(buf.indexOf("<div class=\"obs-table-footer\">")-55, buf.length());
	    weatherBeginIndex = buf.indexOf("<td >");
	    weatherEndIndex = buf.indexOf("</td>		</tr>");
	    weather_1.add(buf.substring(weatherBeginIndex+5,weatherEndIndex));
	    
	    //显示
	    String hour = "      ";
	    String weather = "abc";
	    for(int i=0;i<hours_1.size();i++){	
	    	hour = hours_1.get(i); 		    	
	    	hour = hour.substring(hour.indexOf(":")+1, hour.indexOf(":")+3);
	    	int xxx = Integer.parseInt(hour);
	    	if (xxx == 53){    //判断是否为**：**：53
	    		System.out.print(hours_1.get(i)+";");			    
			    weather = weather_1.get(i).substring(0, 3);
			    if (weather.equals("Fog") ){
			    	System.out.println("Fog");
		    	}else{
		    		System.out.println(weather_1.get(i));
		    	}
	    	}	    		  
	    }
	    	    
	}  
}
