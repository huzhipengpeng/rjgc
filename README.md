#include<stdlib.h>
#include<stdio.h>
#include<iostream.h>
#define NULL 0


typedef struct BookInfo{  /////图书结构
 int b_Code;   ////图书编号
 char b_Name[20]; /////名称
 int b_Total;  /////总数
 int b_Out;  ///借出数
 BookInfo* nextbook;   //////下一类图书
}BookInfo;


typedef struct MemberInfo{  ///会员结构
 long m_Code;  /////会员编号
 char m_Name[20];  ////会员名字
 int l_Codes[6];  /////以借书的编号,最多5
 MemberInfo* nextmember; ////下一会员
}MemberInfo;


typedef struct System{  ///管理系统结构
 BookInfo* BI;    
 MemberInfo* MI;
 int BookTotal;  ////图书类库存量
 int MemberTota; /////会员数量
}System;

System* InitSystem();/////
void AddBook(System*);////增加图书
BookInfo* SearchBook(System*,int);////查询图书信息

void DelBook(System*);/////删除图书

void BrrowBook(System*);///////借书处理
void TurnBackBook(System*);////还书处理

void AddMember(System*);/////添加会员
void DelMember(System*);////删除会员
MemberInfo* SearchMember(System*,int);/////查询会员信息
void StoreData(System*);
void LoadData(System*);
void ExitSystem();
void main()
{
 System* S=InitSystem();
 int sel;
 do{
  cout<<"\n\t\t\t\t图书管理系统"<<endl;
  cout<<"\t\t\t******************************"<<endl;
  cout<<"\t\t\t******************************"<<endl;
  cout<<"\t\t\t 1.增加图书.\t 2.查询图书.\n\t\t\t 3.删除图书.\t 4.借书处理.\n\t\t\t 5.还书处理.\n\t\t\t 6.添加会员.\t 7.删除会员.\n\t\t\t 8.查询会员.\t 9.载入数据.\n\t\t\t10.保存数据.\t11.退出程序.\n";
  cout<<"\t\t\t******************************"<<endl;
  cout<<"\t\t\t******************************"<<endl;
  cout<<"请选择:";
  do{
   cin>>sel;
   if(sel>=1&&sel<=11)break;
   cout<<"选择错误!\n重新输入:"<<endl;
  }while(1);
  switch(sel){
  case 1:AddBook(S);break;
  case 2:SearchBook(S,-1);break;
  case 3:DelBook(S);break;
  case 4:BrrowBook(S);;break;
  case 5:TurnBackBook(S);break;
  case 6:AddMember(S);break;
  case 7:DelMember(S);break;
  case 8:SearchMember(S,-1);break;
  case 9:LoadData(S);break;
  case 10:StoreData(S);break;
  default:ExitSystem();
  }
 }while(1);
}
System* InitSystem()
{
 System* S=(System*)malloc(sizeof(System));
 S->BI=(BookInfo*)malloc(sizeof(BookInfo));
 S->BookTotal=0;
 S->BI->nextbook=NULL;
 S->MI=(MemberInfo*)malloc(sizeof(MemberInfo));
 S->MemberTota=0;
 S->MI->nextmember=NULL;
 return S;
}
void AddBook(System* S)
{
 int Tempcode;
 char sel;
 BookInfo* p=S->BI;
 BookInfo* t;
 BookInfo* m;
 int num;
 do{
  cout<<"输入图书编号:";
  cin>>Tempcode;
  if(m=SearchBook(S,Tempcode)){
   cout<<"这类书以有库存.\n输入图书的入库量:"<<endl;
   cin>>num;
   m->b_Total+=num;
  }
  else{
   t=(BookInfo*)malloc(sizeof(BookInfo));
   t->b_Code=Tempcode;
   cout<<"输入图书的名称:";
   cin>>t->b_Name;
   cout<<"输入图书的入库量:";
   cin>>t->b_Total;
   t->b_Out=0;
   t->nextbook=p->nextbook;
   p->nextbook=t;
   S->BookTotal++;
  }
  cout<<"添加完毕!"<<endl;
  cout<<"还要添加吗?(Y/N)";
  cin>>sel;
  if(sel=='n'||sel=='N'){
   cout<<"结束添加"<<endl;
   return;
  }
 }while(1);
}
BookInfo* SearchBook(System* S,int code){
 BookInfo* bi=S->BI->nextbook;
 int bookcode;
 if(code==-1){
  cout<<"请输入要查询的图书编号:";
  cin>>bookcode;
 }
 else bookcode=code;
 while(bi&&bi->b_Code!=bookcode)bi=bi->nextbook;
 if(code==-1){
  if(!bi)cout<<"没找到你所要的图书."<<endl;
  else {
   cout<<"图书编号为:"<<bi->b_Code<<endl;
   cout<<"图书名称为:"<<bi->b_Name<<endl;
   cout<<"图书库存量为:"<<bi->b_Total<<endl;
   cout<<"图书借出量为:"<<bi->b_Out<<endl;
  }
 }
 return bi;
}
void DelBook(System* S){
 BookInfo* bi;
 BookInfo* pl=S->BI;
 MemberInfo* memi;
 char sel;
 int tempcode;
 int i;
 do{
  pl=S->BI;
  bi=pl->nextbook;
  memi=S->MI->nextmember;
  cout<<"请输入要删除的图书的编号:";
 cin>>tempcode;
  while(bi){
   if(bi->b_Code==tempcode)break;
   pl=bi;
   bi=bi->nextbook;
  }
  if(bi==0)cout<<"没有找到要删除的图书"<<endl;
  else{
   pl->nextbook=bi->nextbook;
   S->BookTotal--;
   while(memi){
    for(i=1;i<=memi->l_Codes[0];i++){
     if(memi->l_Codes[i]==tempcode)break;
    }
    if(i<=memi->l_Codes[0]){
     for(;i<memi->l_Codes[0];i++)memi->l_Codes[i]=memi->l_Codes[i+1];
     memi->l_Codes[0]--;
    }
    memi=memi->nextmember;
   }
   free(bi);
  }
  cout<<"还有图书要删除吗?(Y/N)";
  cin>>sel;
  if(sel=='N'||sel=='n'){
   cout<<"删除图书结束"<<endl;
   return;
  }
 }while(1);
}
void BrrowBook(System* S)
{
 BookInfo* bi=S->BI->nextbook;
 BookInfo* p;
 char sel;
 int memcode;
 MemberInfo* mp;
 int tempcode;
 do{
  cout<<"输入要借出的书号:";
  cin>>tempcode;
  p=SearchBook(S,tempcode);
  if(!p){
   cout<<"没有找到要借出的图书."<<endl;
  }
  else{
   cout<<"此书的现存量为"<<(p->b_Total-p->b_Out)<<endl;
   if(!(p->b_Total-p->b_Out))cout<<"没有足够的书了,外借失败."<<endl;
   else{
    cout<<"请输入会员编号:";
    cin>>memcode;
    mp=SearchMember(S,memcode); 
    if(!mp)cout<<"会员编号输入错误,外借失败"<<endl;
    else{
     if(mp->l_Codes[0]==5)cout<<"借书量不能超过5本";
     else{
      p->b_Out++;
      mp->l_Codes[++mp->l_Codes[0]]=tempcode;
      cout<<"外借成功."<<endl;
     }
    }
   }
  }
  cout<<"\n还有图书要借出吗?(Y/N)";
  cin>>sel;
  if(sel=='N'||sel=='n'){
   cout<<"外借操作结束."<<endl;
   return;
  }
 }while(1);
}
void TurnBackBook(System* S)
{
 BookInfo* bi=S->BI->nextbook;
 BookInfo* p;
 MemberInfo* mp;
 int membercode;
 int tempcode;
 int i;
 char sel;
 do{
  cout<<"输入归还书号:";
  cin>>tempcode;
  p=SearchBook(S,tempcode);
  if(!p){
   cout<<"书号输入错误."<<endl;
  }
  else{
   cout<<"此书的现存量为"<<(p->b_Total-p->b_Out)<<endl;
   cout<<"请输入会员编号:";
   cin>>membercode;
   if(!(mp=SearchMember(S,membercode)))cout<<"会员编号输入错误,归还失败"<<endl;
   else{
    p->b_Out--;
    for(i=1;i<=mp->l_Codes[0];i++){
     if(mp->l_Codes[i]==tempcode)break;
    }
    while(i<mp->l_Codes[0]){
     mp->l_Codes[i]=mp->l_Codes[i+1];
     i++;
    }
    mp->l_Codes[0]--;
    cout<<"归还成功."<<endl;
   }
  }
  cout<<"还有要归还的图书吗?(Y/N)";
  cin>>sel;
  if(sel=='N'||sel=='n'){
   cout<<"归还结束."<<endl;
   return;
  }
 }while(1);
}
void AddMember(System* S)
{
 int Tempcode;
 char sel;
 MemberInfo* p=S->MI;
 MemberInfo* t;
 do{
  cout<<"输入会员编号:";
  cin>>Tempcode;
  t=(MemberInfo*)malloc(sizeof(MemberInfo));
  t->m_Code=Tempcode;
  cout<<"输入会员姓名:";
  cin>>t->m_Name;
  t->l_Codes[0]=0;
  t->nextmember=p->nextmember;
  p->nextmember=t;
  S->MemberTota++;
  cout<<"添加完毕!"<<endl;
  cout<<"还要添加吗?(Y/N)";
  cin>>sel;
  if(sel=='n'||sel=='N'){
   cout<<"结束添加"<<endl;
   return;
  }
 }while(1);
}
MemberInfo* SearchMember(System* S,int code)
{
 MemberInfo* bi=S->MI->nextmember;
 int membercode;
 int i;
 if(code==-1){
  cout<<"请输入要查询的会员编号:";
  cin>>membercode;
 }
 else membercode=code;
 while(bi&&bi->m_Code!=membercode)bi=bi->nextmember;
 if(code==-1){
  if(!bi)cout<<"没找到指定会员."<<endl;
  else {
   cout<<"会员编号为:"<<bi->m_Code<<endl;
   cout<<"名称为:"<<bi->m_Name<<endl;
   cout<<"已借的图书有:"<<bi->l_Codes[0]<<"本."<<endl;
   for(i=1;i<=bi->l_Codes[0];i++)
    cout<<bi->l_Codes[i]<<' ';
   cout<<endl;
  }
 }
 return bi;
}
void DelMember(System* S)
{
 MemberInfo* bi;
 MemberInfo* pl;
 BookInfo* book;
 char sel;
 int i;
 int tempcode;
 do{
  bi=S->MI->nextmember;
  pl=S->MI;
  cout<<"请输入要删除的会员的编号:";
  cin>>tempcode;
  while(bi){
   if(bi->m_Code==tempcode)break;
   pl=bi;
   bi=bi->nextmember;
  }
  if(!bi)cout<<"没有找到要删除的会员编号.";
  else{
   pl->nextmember=bi->nextmember;
   S->MemberTota--;
   for(i=1;i<=bi->l_Codes[0];i++){
    if(!(book=SearchBook(S,bi->l_Codes[i]))){
     cout<<"删除会员出错!"<<endl;
    }
    else{
     book->b_Out--;
     book->b_Total--;
    }
   }
   free(bi);
  }
  cout<<"还有会员要删除吗?(Y/N)";
  cin>>sel;
  if(sel=='N'||sel=='n'){
   cout<<"删除会员结束"<<endl;
   return;
  }
 }while(1);
}
void StoreData(System* S){
 FILE* fp;
 BookInfo* bi=S->BI->nextbook;
 if(!(fp=fopen("BookSys","wb"))){
  cout<<"打开文件BookSys失败!"<<endl;
  exit(0);
 }
 fwrite(&(S->BookTotal),sizeof(int),1,fp);
 while(bi){
  fwrite(bi,sizeof(BookInfo),1,fp);
 bi=bi->nextbook;
 }
 MemberInfo* mi=S->MI->nextmember;
 fwrite(&(S->MemberTota),sizeof(int),1,fp);
 while(mi){
  fwrite(mi,sizeof(MemberInfo),1,fp);
  mi=mi->nextmember;
 }
 fclose(fp);
}
void LoadData(System* S){
 FILE* fp;
 if(!(fp=fopen("BookSys","rb"))){
  cout<<"打开文件BookSys失败"<<endl;
  exit(0);
 }
 BookInfo* bi=S->BI;
 BookInfo* tempbi;
 fread(&(S->BookTotal),sizeof(int),1,fp);
 for(int i=1;i<=S->BookTotal;i++){
 tempbi=(BookInfo*)malloc(sizeof(BookInfo));
  fread(tempbi,sizeof(BookInfo),1,fp);
  bi->nextbook=tempbi;
 bi=tempbi;
 }
 bi->nextbook=NULL;
 MemberInfo* mi=S->MI;
 MemberInfo* tempmi;
 fread(&(S->MemberTota),sizeof(int),1,fp);
 for(i=1;i<=S->MemberTota;i++){
  tempmi=(MemberInfo*)malloc(sizeof(MemberInfo));
  fread(tempmi,sizeof(MemberInfo),1,fp);
  mi->nextmember=tempmi;
  mi=tempmi;
 }
 mi->nextmember=NULL;
 fclose(fp);
}
void ExitSystem(){
 char select;
 cout<<"警告: 程序结束后未存储的数据将消失."<<endl;
 cout<<"确定要退出吗?(Y/N)";
 cin>>select;
 if(select=='y'||select=='Y')exit(0);
 if(select=='n'||select=='N')return;
}
