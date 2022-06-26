#include<stdio.h>
#include<stdlib.h>
#include<string.h>
#include<time.h>
#include<windows.h>

struct TransactionRecord
{
	char username[100];
	time_t timestamp;//时间戳	
	int type;//交易类型 0-取钱，1-存钱，2-转出，3-转入
	int amount;//数额 
	
	struct TransactionRecord * next; 
}; 
typedef struct TransactionRecord TR;

TR * trHead=NULL;
TR * trTail=NULL; 

struct Account
{
	char username[100];
    int  idCard[19];//身份证号
	char password[100];//密码
	int tel[12];//电话号码
	float money;
	float money1;
	int zh;//账号
	char flag[6];//卡的状态
};


typedef struct Account Account;

Account user[1000];
int  userCount=0;
 
int curAccountIndex;
int otherAccountIndex;

void showMenu();//菜单

void showChinese();//中文菜单

void showEnglish();//英文菜单

void Transfer();//转账

void findOtherAccount();//查找

int findAccount(Account a);//查找账户

void saveTransactionRecord();//保存交易记录

void Chinese();//中文语言

void English();//英文

void closeaccount();//销户

void language();//语言选择

void searchAccoun();//根据卡号查找指定账户所在下标

void exit();//退出

int searchAccoun(int accNumber)
{
	int i,pos=-1;
	for(i=0;i<userCount;i++)
	{
		if(user[i].zh==accNumber&&strcmp(user[i].flag,"销户")!=0)//找到这个张卡了
		{
			pos=i;
			break;
		}
	}
	return pos;//返回下标
}

void closeaccount()
{
	printf("欢迎进入销户系统\n");
	printf("Welcome to the account cancellation system\n");
	int accNUm;//卡号
	int pos;
	int userAnswer;
	int answer;
	char wrd[20]={0};
	printf("请输入您要销户的账号\n");
	scanf("%d",&accNUm);
	printf("请输入账号密码\n");
	scanf("%s",wrd);
	pos=searchAccoun(accNUm);
	if(pos==0)
	{
		printf("卡号不存在，请重新选择您要办理的业务！\n");
		language();
	}
	else
	{
		if(strcmp(user[pos].password,wrd)!=0)
		{
			printf("您的密码有误\n");
			printf("按继续办理业务，按其他数字返回主界面！\n");
			scanf("%d",&answer);
			if(answer==1)
			{
				closeaccount();
			}
			else
			{
				language();
			}
		}
		else
		{
			printf("确实注销此账户？\n");
			printf("按1销户，按其他数字返回主界面!\n");
			scanf("%d",&userAnswer);
			if(userAnswer==1)
			{
				user[pos].money=0;
				strcpy(user[pos].flag,"销户");
				printf("销户成功。按任意键返回！");
				fflush(stdin);//清除缓存区
				getchar();//任意键返回
				language();
			}
			else
			{
				printf("您已取消销户，按任意键返回\n");
				fflush(stdin);//清除缓存区
				getchar();//任意键返回
				language();
			}
		}
	}
}
//找到返回1，否则返回0 
int findAccount(Account a)
{
	for(int i=0;i<userCount;i++)
	{	
		if((user[i].zh==a.zh)&&(strcmp(user[i].password,a.password)==0))
		{
			curAccountIndex=i;
			return 1;
		}
	}
	return 0;
}






void updatePassword()
{


    printf("*******************************************\n\n");
	printf("         下面将进行的操作!\n\n");
    printf("*******************************************\n\n");

	printf("请输入旧密码:\n");
	char oldPassword[100]={'\0'};

	scanf("%s",oldPassword);
	if(strcmp(oldPassword,user[curAccountIndex].password)==0)
	{
		//让其修改 
		printf("请输入新密码：\n");
		scanf("%s",user[curAccountIndex].password);
		printf("修改成功！\n");
	}
	else
	{
		printf("旧密码输入错误，拒绝修改！\n");
	} 

    
}




void saveMoney()
{

    printf("*******************************************\n\n");
	printf("            下面将进行的操作!\n\n");
    printf("*******************************************\n\n");

	printf("请输入存款金额：\n");
	float money;
	scanf("%f",&money);
	//判断合理性

	printf("存款成功！\n");
	user[curAccountIndex].money+=money;


    
}


void saveTransactionRecord()
{
	FILE* fp=fopen("D:/transation_record.txt","w");
	TR* curP=trHead;
	while(curP!=NULL)
	{
		fprintf(fp,"%s %ld %d %f\n",curP->username,curP->timestamp,curP->type,curP->amount);
		curP=curP->next;
	}
	fclose(fp);
}  



void drawMoney()
{
    printf("*******************************************\n\n");
	printf("           下面将进行的操作!\n\n");
    printf("*******************************************\n\n");

	printf("请输入取款金额：");

	int money;
	scanf("%d",&money);

	if(user[curAccountIndex].money<money)
	{
		printf("余额不足!\n");
	}
	else
	{
		printf("取款成功!\n");
	}

	user[curAccountIndex].money-=money;
	printf("当前余额为:%f\n",user[curAccountIndex].money);

	
}
void Chinese()
{
		printf("请选择你所需要的操作：\n");
		printf("1.修改密码\t 2.取款\t 3.存款\t 4.转账\t5.销户\t6.退出\t\n");
}
void English()
{
		printf("Please select the action you want:\n");
		printf("1.Change Password\t 2.withdraw money\t 3.deposit\t4.transfer accounts\t5.closeaccount\t6.exit\t\n");
}

void language()
{
	int T;
	printf("您好女士/先生，请选择语言\n输入0是中文\n输入1是英文\n");
	printf("Hello, madam / sir, please select a language. Enter C as Chinese and E as English\n");
	scanf("%d",&T);
	switch(T)
	{
	case 0:Chinese();break;
	case 1:English();break;
	}
}

void showMenu()
{
    	int j;
		language();
		while(1)
		{
		scanf("%d",&j);

	    switch(j)
		{
		case 1:updatePassword();break;
		case 2:drawMoney();break;
		case 3:saveMoney();break;
		case 4:Transfer();break;
		case 5:closeaccount();break;
		case 6:exit();break;
		}
		if(j==5)
			break;
		}
	    printf("\n");
	    system("cls");
		
}


int findOtherAccount(int otherUsername)
{
	for(int i=0;i<userCount;i++)
	{
		if(user[i].zh==otherUsername)
		{
			otherAccountIndex=i;
			return 1;
		}
	}

	system("pause");
	return 0;


}



void homePage()
{
	system("cls");
    showMenu();

}



int order=0;


void signIn()
{
	
		Account a; 
        printf("*******************************************\n\n");
		printf("             欢迎登录\n\n");
        printf("*******************************************\n\n");
	
		printf("请输入账号：\n");
		scanf("%d",&a.zh);
		
		printf("请输入密码：\n");
		scanf("%s",a.password);
		if(findAccount(a))
		{
			homePage();
			return;
		}
		else
		{
			printf("登录失败!\n");
		}

}

void signUp1()
{
	printf("请输入姓名：\n");
	scanf("%s",user[userCount].username);

	printf("请输入身份证号：\n");
	scanf("%s",user[userCount].idCard);

	printf("请输入密码：\n");
	scanf("%s",user[userCount].password);

	printf("请输入电话号码：\n");
	scanf("%s",user[userCount].tel);

    user[userCount].zh = 1000 + userCount;

	printf("恭喜您,开户成功!\n\n\n");

	printf("您的账号是：%d\n\n\n\n",user[userCount].zh);

	user[userCount].money=0.0f;

	userCount++;
	
}

void signUp()
{

    printf("*******************************************\n\n");
	printf("               欢迎使用!\n\n");
	printf("*******************************************\n\n");
    
    printf("请选择:\n\n1.中文    2.English(英文)\n");
	char c;
	scanf("%c",&c);
	if(c=='1')
	{
		showChinese();//显示中文菜单
	}
	else if(c=='2')
	{
		showEnglish();//显示英文菜单
	}	
}


void showChinese()
{  
	printf("*******************************************\n\n");
    printf("\t1.创建账户  2.登录\n\n");
    printf("*******************************************\n\n");

	
	int m;
	scanf("%d",&m);
	if(m==1)
	{
		signUp1();
	}
	else if(m==2)
	{
		signIn();
	}
}

void showEnglish()
{
    printf("*******************************************\n\n");
    printf("\t\t1.Create account  2.Sign in\n\n");
    printf("*******************************************\n\n");
	int m;
	scanf("%d",&m);
	if(m==1)
	{
		signUp1();
	}
	else if(m==2)
	{
		signIn();
	}
	
}

void Transfer()
{
	int otherUsername;

	//输入对方账户
	printf("请输入对方账户：");
	
	scanf("%d",&otherUsername);

	//验证账号的合法性
	if(findOtherAccount(otherUsername))
	{
		int money;

		//输入转账金额
		printf("请输入转账金额：");
		
		scanf("%d",&money);

		//验证金额合法性
		if((user[curAccountIndex].money)>=money)
		{
			//记录转账记录：转出(创建交易记录结点，初始化，添加到链表)
			TR * newNodeOut=(TR*)malloc(sizeof(TR));

			//转账（原账户的钱减少，对方账户的钱增多）
			user[curAccountIndex].money-=money;
			user[otherAccountIndex].money+=money;

			//输出提示转账成功
			printf("转账成功！\n");

			printf("当前账户余额为%f\n",user[curAccountIndex].money);
			printf("对方账户余额为%f\n",user[otherAccountIndex].money);



			
		
			newNodeOut->next=NULL;
			strcpy(newNodeOut->username,user[curAccountIndex].username);
			newNodeOut->type=2;
			newNodeOut->amount=money;
			newNodeOut->timestamp=time(NULL);
			
			if(trHead==NULL)
			{
				trHead=newNodeOut;
				trTail=newNodeOut; 
			}
			else
			{
				trTail->next=newNodeOut;
				trTail=newNodeOut;
			}


			//记录转账记录：转入
			TR * newNodeIn=(TR*)malloc(sizeof(TR));
		
			newNodeIn->next=NULL;
			strcpy(newNodeIn->username,user[curAccountIndex].username);
			newNodeIn->type=3;
			newNodeIn->amount=money;
			newNodeIn->timestamp=time(NULL);
			
			if(trHead==NULL)
			{
				trHead=newNodeIn;
				trTail=newNodeIn; 
			}
			else
			{
				trTail->next=newNodeIn;
				trTail=newNodeIn;
			}
		}
		else
		{
			printf("余额不足!\n");
		}
	}
	else
	{
		printf("账户错误！\n");
	}
	
}






int loadData()
{
	FILE* fp=fopen("D:/atm.txt","r");
	if(fp==NULL)
	{
		return 0; 
	}
	else
	{
		while(!feof(fp))
		{
			fscanf(fp,"%d %s %f\n",&user[userCount].zh,user[userCount].password,&user[userCount].money);
			userCount++;
		}
		return 1;
	}

}

void saveData()
{
	FILE * fp=fopen("D:/atm.txt","w");
	for(int i=0;i<userCount;i++)
	{
		fprintf(fp,"%d %s %f\n",user[i].zh,user[i].password,user[i].money);
	}
	fclose(fp);
}
void textcolor(int color)
{
	HANDLE hc=GetStdHandle(STD_OUTPUT_HANDLE);
	SetConsoleTextAttribute(hc,0|color);
}
void exit()
{
	printf("感谢使用此ATM\n");
	printf("欢迎下次光临\n");
	printf("Thank you for using this ATM\n");
	printf("looking forward to your next visit\n");
}
int main()
{
	int color=3;
	textcolor(color);
	int status=loadData();
	if(status==1)
	{
		printf("加载成功！\n");
	}
	else
	{
		printf("加载失败！\n");
	}

    time_t timestamp=time(NULL);
    printf("%ld\n",timestamp);
   
	char * ct=ctime(& timestamp);
    printf("%s\n",ct);
	signUp();
    for(int i=0;i<userCount;i++)
	{
		printf("ii%d %s\n",user[i].zh,user[i].password);
	}

	signIn();

	saveData();
	system("pause");
	return 0;
}
