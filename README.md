#include<stdio.h>
#include<stdlib.h>
#include<iostream>
using namespace std;
#define Max 9999999
typedef struct {
	int weight;
}Arc,**Adj;
typedef struct{
	int *vex;
	Adj arcs;
	int vexnum;
	int arcnum;
}MGraph;                //图
typedef struct {
	int Destination;
	int nexthop;
	int length;
}RoutingTable,**RT;      //路由表
RT Rout;
int NETWORK_SIZE;
double PROBABILITY_OF_EAGE;
MGraph G;
int flag;
void initial()                                               //图分配空间
{
	G.vexnum=NETWORK_SIZE;
	if(!(G.vex=(int *)malloc((NETWORK_SIZE + 1)*sizeof(int *))))
	{
		cout << "邻接矩阵内存分配错误" << endl;
		exit(0);
	}
	if (!(G.arcs= (Adj)malloc(sizeof(int *) * (NETWORK_SIZE + 1))))
	{
		cout << "邻接矩阵内存分配错误" << endl;
		exit(0);
	}
	for(int i=0;i<G.vexnum;i++)
		G.arcs[i]=(Arc *)malloc(sizeof(int)*G.vexnum);
	for(int i=0;i<G.vexnum;i++)
		G.vex[i]=i+1;
	G.arcnum=0;
}
void generateRandomNetwork() {                              //构建网络
	int i, j;
	double probability = 0.0;
	int count=0;
	for (i = 0; i <G.vexnum; i++)
		for (j = 0; j <G.vexnum;j++)
			G.arcs[i][j].weight=Max;
	for (i = 0; i < NETWORK_SIZE; i++)
	{
		for (j = 0; j < NETWORK_SIZE; j++)
		{
			probability = (rand() % NETWORK_SIZE) / (double)NETWORK_SIZE*2;//生成一个随机数
			if (i!=j&&probability < PROBABILITY_OF_EAGE&&G.arcs[i][j].weight==Max)//如果此随机数小于连边概率，则在此（i，j)节点对之间添加一条边，否则不添加边。
			{
				G.arcnum++;
				 G.arcs[i][j].weight =1;
				 G.arcs[j][i].weight=G.arcs[i][j].weight;
			}
		}
	}
}
void printf(){
	int i,j,n=0;
	printf("                                                                            已成功生成网络 正在打印.......\n                                                                                ");
	for(i=0;i<G.vexnum;i++){
		if(G.vex[i]<10)
		printf("%d    ",G.vex[i]);
		else 
			printf("%d   ",G.vex[i]);
	}
		printf("\n");
	for(i=0;i<G.vexnum;i++){
		if(G.vex[i]<10)
		printf("                                                                            %d   ",G.vex[i]);
		else
        printf("                                                                            %d  ",G.vex[i]);
		for(j=0;j<G.vexnum;j++)
			{
				if(G.arcs[i][j].weight==Max) printf("∞   ");
				else{
					n++;
				printf("%d    ",G.arcs[i][j].weight);
				}
				if(j==G.vexnum-1) printf("\n");
		}
	}
	G.arcnum=n/2;
}
void creat(){
	printf("请输入构建的网规模:");
	scanf("%d",&NETWORK_SIZE);
	T:printf("请输入网络连边概率(0~1任意小数):");
	scanf("%lf",&PROBABILITY_OF_EAGE);
	if(PROBABILITY_OF_EAGE<0||PROBABILITY_OF_EAGE>1) {
		printf("请输入正确概率\n");
		goto T;
	}
	initial();
    generateRandomNetwork();
	printf();
	Rout=(RT)malloc(sizeof(int)*(1+G.vexnum));
	for(int j=0;j<G.vexnum;j++)
		Rout[j]=(RoutingTable *)malloc(sizeof(int)*(1+G.vexnum));
}
void Dijkstra(int v0,int path[],int distance[]){
	int n=G.vexnum;
	int *S=(int *)malloc(sizeof(int)*n);
	int minDis,i,j,u=0;
	for(i=0;i<n;i++)
	{
		distance[i]=G.arcs[v0][i].weight;
		S[i]=0;
		if(i!=v0&&distance[i]<Max)
		         path[i]=v0;
		else path[i]=-1;
	}
	S[v0]=1;
	for(i=1;i<n;i++)
	{
		minDis=Max;
		for(j=0;j<n;j++)
			if(S[j]==0&distance[j]<minDis)
			{
				u=j;
				minDis=distance[j];
			}
			
			S[u]=1;
			for(j=0;j<n;j++)
				if(S[j]==0&&G.arcs[u][j].weight<Max&&distance[u]+G.arcs[u][j].weight<distance[j])
			{
				    distance[j]=distance[u]+G.arcs[u][j].weight;
					path[j]=u;
			}
	}
	for(i=0;i<n;i++)
	{
		if(G.vex[i]==G.vex[v0]) continue;
		j=i;
		while(path[j]!=v0)
		{
			j=path[j];
			if(j==-1) break;
		}
		Rout[v0][i].Destination=G.vex[i];
		Rout[v0][i].nexthop=G.vex[j];
	}
	for(j=0;j<G.vexnum;j++)
		Rout[v0][j].length=distance[j];
	}
void putList()
{
	int i,j,flag=0,z;
	int *path,*distance;
	path=(int *)malloc(sizeof(int)*G.vexnum);
	printf("\n请输入要查询的路由器编号:");
	scanf("%d",&i);
	for(z=0;z<G.vexnum;z++)
		if(G.vex[z]==i) 
			{
				flag=1;
				break;
		}
	if(flag==0) {
		printf("                                                                            !!!您查询的路由不存在!!!\n");
		return ;
	}
	distance=(int *)malloc(sizeof(int)*G.vexnum);
	Dijkstra(z,path,distance);
	printf("路由表打印中......\n");
	printf("                                                                            ~~~目标路由      下一跳路由~~~\n");
	for(j=0;j<G.vexnum;j++){
		if(G.vex[j]==G.vex[z]) 
			continue;
		if(Rout[z][j].nexthop!=0&&Rout[z][j].nexthop>0)
		printf("                                                                                   %d            %d\n",Rout[z][j].Destination,Rout[z][j].nexthop);
	}
		for(j=0;j<G.vexnum;j++){
			if(G.vex[j]==G.vex[z]) continue;
			if(Rout[z][j].length>=16) Rout[z][j].length=Max;
			if(Rout[z][j].length==Max) printf("                                                                                到路由%d不可达\n",Rout[z][j].Destination);
		else printf("                                                                                到路由%d最短路径长度为:%d\n",Rout[z][j].Destination,Rout[z][j].length);
		}
		}
void deletpoint(){
	printf("目前有%d个路由,请输入合法路由数\n",G.vexnum);
	int x,y;
	printf("请输入您要删除的路由数:");
	scanf("%d",&x);
	if(x>0&&x<=G.vexnum){
	for(y=0;y<x;y++)
	{
	printf("\n请输入要删除的路由编号:");
	int n,flag=0,i,j,k;
	scanf("%d",&n);
	for(i=0;i<G.vexnum;i++)
		if(G.vex[i]==n){
			flag=1;
			break;
		}
	if(flag==0)
	{
		printf("                                                                             !!!您要删除的路由不存在!!!\n");
			return ;
	}
	k=i;

	for(j=0;j<G.vexnum;j++)
		for(i=k;i<G.vexnum-1;i++)
		G.arcs[j][i].weight=G.arcs[j][i+1].weight;
	for(i=k;i<G.vexnum-1;i++)
		for(j=0;j<G.vexnum;j++)
			G.arcs[i][j].weight=G.arcs[i+1][j].weight;

	for(i=k;i<G.vexnum;i++)
	{
		G.vex[i]=G.vex[i+1];
	}

	G.vexnum--;
	}
	printf("删除成功\n");
	}
	else printf("目前有%d个路由,请输入合法路由数\n",G.vexnum);
}
void deletarc(){
	printf("目前网络有%d条边,请输入合法条数\n",G.arcnum);
	printf("请输入您要删除的边数:");
	int x;
	scanf("%d",&x);
	if(x>0&&x<G.arcnum){
	for(int y=0;y<x;y++)
	{
	printf("\n请输入要删除的边的前后路由(格式a b):");
	int a,b,i,flag1=0,flag2=0,j;
	scanf("%d %d",&a,&b);
	for(i=0;i<G.vexnum;i++)
		if(G.vex[i]==a){
			flag1=1;
			break;
		}
		if(flag1==0){
			printf("                                                                            !!!您输入的%d号路由器不存在!!!\n",a);
			return ;
		}
    for(j=0;j<G.vexnum;j++)
		if(G.vex[j]==b){
			flag2=1;
			break;
		}
		if(flag2==0){
			printf("                                                                            ！！！您输入的%d号路由器不存在!!!\n",b);
			return ;
		}
	G.arcs[i][j].weight=Max;
	G.arcs[j][i].weight=Max;
	G.arcnum--;
	}
	printf("删除成功\n");
	}
	else printf("目前网络有%d条边,请输入合法条数\n",G.arcnum);
}
void addarc(){
	printf("目前网络有%d条边,请输入合法条数\n",G.arcnum);
	printf("请输入您要添加的边数:");
	int x;
	scanf("%d",&x);
	if((x+G.arcnum)<=(G.vexnum*(G.vexnum-1)/2)){
	for(int y=0;y<x;y++)
	{
	printf("\n请输入要添加的边的前后路由(格式a b):");
	int a,b,i,j,flag1=0,flag2=0;
	scanf("%d %d",&a,&b);
	for(i=0;i<G.vexnum;i++)
		if(G.vex[i]==a){
			flag1=1;
			break;
		}
		if(flag1==0){                                                                                
			printf("                                                                            ！！！！您输入的%d号路由器不存在！！！！\n",a);
			return ;
		}
    for(j=0;j<G.vexnum;j++)
		if(G.vex[j]==b){
			flag2=1;
			break;
		}
		if(flag2==0){
			printf("                                                                            ！！！您输入的%d号路由器不存在！！！\n",b);
			return ;
		}
	G.arcs[i][j].weight=1;
	G.arcs[j][i].weight=1;
	G.arcnum++;
	}
	printf("添加成功");
	}
	else printf("目前网络有%d条边,请输入合法条数\n",G.arcnum);
}

void Rush(){
	Q1:printf("                                                                               **网络路由更新**\n\n                                                                                0.返回\n\n                                                                                1.删除边\n\n                                                                                2.删除点\n\n                                                                                3.添加边\n\n                                                                                4.路由表查询\n\n                                                                                5.显示网络\n");
    Q:printf("请输入合法选项:");
	int n;
	scanf("%d",&n);
	if(n!=0&&n!=1&&n!=2&&n!=3&&n!=4&&n!=5)
		goto Q;
	switch(n){
	case 1:deletarc();break;
	case 2:deletpoint();break;
	case 3:addarc();break;
	case 4:putList();break;
	case 5:printf();break;
	case 0:return ;break;
	}
	
	goto Q1;
}
		
void menu(){
	Z:printf("                                                                                *******欢迎来到本系统*******\n                                                                                     请根据菜单选择功能\n");
	printf("                                                                                0.退出\n\n                                                                                1.构建广域网\n\n                                                                                2.查询路由表\n\n                                                                                3.路由更新\n\n");
	L:printf("请输入合法选项:");
	int n;
	scanf("%d",&n);
	if(n==0||n==1||n==2||n==3){
		if((n!=1&&n!=0)&&flag==0) {
			printf("                                                                  !!!!!!您还未构建广域网，不可进行其他操作!!!!!!\n");
			goto L;
		}
	switch(n){
	case 1:flag=1;creat();break;
    case 2:putList();break;
	case 3:Rush();break;
	case 0:exit(0);
	}
	}
	else goto L;
	printf("\n返回中......\n");
	goto Z;	

}
int main(){
	menu();
	return 0;
}

