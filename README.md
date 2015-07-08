

import java.util.Calendar;
import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.io.*;
import java.util.Hashtable;
public class CalendarPad extends JFrame implements MouseListener
{
   int year,month,day;
   Hashtable hashtable;             
   File file;                       
   JTextField showDay[];             
   JLabel title[];                   
   Calendar 日历;
   int 星期几; 
   NotePad notepad=null;             
   Month 负责改变月;
   Year  负责改变年;
   String 星期[]={"星期日","星期一","星期二","星期三","星期四","星期五","星期六"};
   JPanel leftPanel,rightPanel;    
   public  CalendarPad(int year,int month,int day)
   { 
     leftPanel=new JPanel();
     JPanel leftCenter=new JPanel();
     JPanel leftNorth=new JPanel();
     leftCenter.setLayout(new GridLayout(7,7));   
                                                  
     rightPanel=new JPanel();
     this.year=year;
     this.month=month;
     this.day=day;
     负责改变年=new Year(this);
     负责改变年.setYear(year);
     负责改变月=new Month(this);
     负责改变月.setMonth(month);
  
     title=new JLabel[7];                         
     showDay=new JTextField[42];                   
     for(int j=0;j<7;j++)                         
       {
         title[j]=new JLabel();
         title[j].setText(星期[j]);
         title[j].setBorder(BorderFactory.createRaisedBevelBorder());
         leftCenter.add(title[j]);
       } 
     title[0].setForeground(Color.red);
     title[6].setForeground(Color.blue);

     for(int i=0;i<42;i++)                        
       {
         showDay[i]=new JTextField();
         showDay[i].addMouseListener(this);
         showDay[i].setEditable(false);
         leftCenter.add(showDay[i]);
       }
         
     日历=Calendar.getInstance();
     Box box=Box.createHorizontalBox();          
     box.add(负责改变年);
     box.add(负责改变月);
     leftNorth.add(box);
     leftPanel.setLayout(new BorderLayout());
     leftPanel.add(leftNorth,BorderLayout.NORTH);
     leftPanel.add(leftCenter,BorderLayout.CENTER);
     leftPanel.add(new Label("请在年份输入框输入所查年份(负数表示公元前),并回车确定"),
                  BorderLayout.SOUTH) ;
     leftPanel.validate();
     Container con=getContentPane();
     JSplitPane split=new JSplitPane(JSplitPane.HORIZONTAL_SPLIT,
                                     leftPanel,rightPanel);
     
     con.add(split,BorderLayout.CENTER);
     con.validate();
    
     hashtable=new Hashtable();
     file=new File("日历记事本.txt");
      if(!file.exists())
      {
       try{
           FileOutputStream out=new FileOutputStream(file);
           ObjectOutputStream objectOut=new ObjectOutputStream(out);
           objectOut.writeObject(hashtable);
           objectOut.close();
           out.close();
          }
       catch(IOException e)
          {
          }
      } 
    
     notepad=new NotePad(this);                                      
     rightPanel.add(notepad);
     
     设置日历牌(year,month);
     addWindowListener(new WindowAdapter()
                    { public void windowClosing(WindowEvent e)
                       {
                         System.exit(0);
                       }
                    });
    setVisible(true);
    setBounds(100,50,524,285);
    validate();
   }
  public void 设置日历牌(int year,int month)
   {
     日历.set(year,month-1,1);              
     
     星期几=日历.get(Calendar.DAY_OF_WEEK)-1;
     if(month==1||month==2||month==3||month==5||month==7
                        ||month==8||month==10||month==12)
        {   排列号码(星期几,31);         
        }
     else if(month==4||month==6||month==9||month==11)
        {  排列号码(星期几,30);
        }
     else if(month==2)
        {   if((year%4==0&&year%100!=0)||(year%400==0))  
           {   排列号码(星期几,29);
           }
         else
           {   排列号码(星期几,28);
           }
       }
   } 
  public void 排列号码(int 星期几,int 月天数)
   {
      for(int i=星期几,n=1;i<星期几+月天数;i++)
             {   showDay[i].setText(""+n);
               if(n==day)
                 {   showDay[i].setForeground(Color.green); 
                     showDay[i].setFont(new Font("TimesRoman",Font.BOLD,20));
                 }
               else
                 {   showDay[i].setFont(new Font("TimesRoman",Font.BOLD,12));
                      showDay[i].setForeground(Color.black);
                 }
               if(i%7==6)
                 {   showDay[i].setForeground(Color.blue);  
                 }
               if(i%7==0)
                 {  showDay[i].setForeground(Color.red);  
                 }
               n++; 
             }
       for(int i=0;i<星期几;i++)
             {  showDay[i].setText("");
             }
       for(int i=星期几+月天数;i<42;i++)
             {   showDay[i].setText("");
             }
   }
 public int getYear()
   {   return year;
   } 
 public void setYear(int y)
   {   year=y;
     notepad.setYear(year);
   }
 public int getMonth()
   {  return month;
   }
 public void setMonth(int m)
   {  month=m;
      notepad.setMonth(month); 
   }
 public int getDay()
   {   return day;
   }
 public void setDay(int d)
   {   day=d;
       notepad.setDay(day);
   }
 public Hashtable getHashtable()
   {   return hashtable;
   }
 public File getFile()
   {   return file;
   }
 public void mousePressed(MouseEvent e)             
   {  JTextField source=(JTextField)e.getSource();
      try{
         day=Integer.parseInt(source.getText());
         notepad.setDay(day);
         notepad.设置信息条(year,month,day);
         notepad.设置文本区(null);
         notepad.获取日志内容(year,month,day);
        } 
      catch(Exception ee)
        {
        }
   }
 public void mouseClicked(MouseEvent e)
   {
   }
 public void mouseReleased(MouseEvent e)
   {
   }
 public void mouseEntered(MouseEvent e)
   {
   }
 public void mouseExited(MouseEvent e)
   {
   }
 public static void main(String args[])
   {   Calendar calendar=Calendar.getInstance();    
       int y=calendar.get(Calendar.YEAR);           
       int m=calendar.get(Calendar.MONTH)+1;        
       int d=calendar.get(Calendar.DAY_OF_MONTH);
       new CalendarPad(y,m,d);
   }
}  
