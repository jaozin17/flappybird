#include <iostream>
#include <conio.h>
#include <dos.h>
#include <stdlib.h>
#include <string.h>
#include <windows.h>
#include <time.h>
#include <locale.h>
//----------------------------------------------------
#define tela_larg 90
#define tela_alt 26
#define jan_larg 70
#define menu_larg 20
#define buraco_tam 7
#define Cano_dif 45
#define ANSI_COLOR_GREEN   "\x1b[32m"
#define ANSI_COLOR_RESET   "\x1b[0m"
#define ANSI_COLOR_YELLOW  "\x1b[33m"
#define ANSI_COLOR_RED     "\x1b[31m"
#define ANSI_COLOR_ORANGE  "\x1b[38;5;208m"
#define ANSI_COLOR_PURPLE  "\x1b[38;5;93m"
#define ANSI_COLOR_BLUE    "\x1b[34m"
//----------------------------------------------------
using namespace std;
//----------------------------------------------------
HANDLE console = GetStdHandle(STD_OUTPUT_HANDLE);
COORD CursorPosition;
//----------------------------------------------------
int CanoPos[3];
int buracoPos[3];
int CanoFlag[3];
char Passaro[2][6] = { '/','-','-','ò','\\',' ',
					'\\','_',',','_','/','>' };
int PassaroPos = 6;
int score = 0;
int pulo;
char cor='1';
int record;
//----------------------------------------------------
void gotoxy(int x, int y){
	CursorPosition.X = x;
	CursorPosition.Y = y;
	SetConsoleCursorPosition(console, CursorPosition);
}
//----------------------------------------------------
void setcursor(bool visible, DWORD tam) {
	if(tam == 0)
		tam = 20;	
	
	CONSOLE_CURSOR_INFO lpCursor;	
	lpCursor.bVisible = visible;
	lpCursor.dwSize = tam;
	SetConsoleCursorInfo(console,&lpCursor);
}
//----------------------------------------------------
void DesBorder(){ 
//desenha a borda onde acontece o jogo
	int i;
	
	for(i=0; i<tela_larg; i++){
		gotoxy(i,0); cout<<"±"; //lado de cima
		gotoxy(i,tela_alt); cout<<"±"; //lado de baixo
	}
	
	for(i=0; i<tela_alt; i++){
		gotoxy(0,i); cout<<"±"; //lado esquerdo
		gotoxy(tela_larg,i); cout<<"±"; //lado direito
	}
	for(i=0; i<tela_alt; i++){
		gotoxy(jan_larg,i); cout<<"±"; //meio
	}
}
//----------------------------------------------------
void genCano(int ind){
	buracoPos[ind] = 3 + rand()%14;//gerando os espaços
}
//----------------------------------------------------
void DesCano(int ind) {
    // desenho dos Canos
    if (CanoFlag[ind] == true) {
        for (int i = 0; i < buracoPos[ind]; i++) {
            gotoxy(jan_larg - CanoPos[ind], i + 1);
            cout << ANSI_COLOR_GREEN "***" ANSI_COLOR_RESET;
        }
        for (int i = buracoPos[ind] + buraco_tam; i < tela_alt - 1; i++) {
            gotoxy(jan_larg - CanoPos[ind], i + 1);
            cout << ANSI_COLOR_GREEN "***" ANSI_COLOR_RESET;
        }
    }
}
//----------------------------------------------------
void ApagaCano(int ind) {
    if (CanoFlag[ind] == true) {
        for (int i = 0; i < buracoPos[ind]; i++) {
            gotoxy(jan_larg - CanoPos[ind], i + 1);
            printf("   ");
        }
        for (int i = buracoPos[ind] + buraco_tam; i < tela_alt - 1; i++) {
            gotoxy(jan_larg - CanoPos[ind], i + 1);
            printf("   ");
        }
    }
}
//----------------------------------------------------
void DesPassaro() {
	int i;
	int j;
    if(cor=='1'){
	int i, j;
    for (i = 0; i < 2; i++) {
        for (j = 0; j < 6; j++) {
        	gotoxy(j + 2, i + PassaroPos);
        	
            if (((i==1) && (j==5)) || ((i==1) && (j==2))) {
            	cout << ANSI_COLOR_RED << Passaro[i][j] << ANSI_COLOR_RESET;
			} else if ((i==0) && (j==3)) {
				cout << ANSI_COLOR_RESET << Passaro[i][j] << ANSI_COLOR_RESET;
			} else {
            cout << ANSI_COLOR_YELLOW << Passaro[i][j] << ANSI_COLOR_RESET;
        }
        }
    }
}
	if(cor=='2'){
	for (i = 0; i < 2; i++) {
        printf("\n");
        for (j = 0; j < 6; j++) {
			gotoxy(j + 2, i + PassaroPos);	
            if (((i==1) && (j==5)) || ((i==1) && (j==2))) {
            	cout << ANSI_COLOR_ORANGE << Passaro[i][j] << ANSI_COLOR_RESET;
			} else if ((i==0) && (j==3)) {
				cout << ANSI_COLOR_RESET << Passaro[i][j] << ANSI_COLOR_RESET;	
			} else {
            cout << ANSI_COLOR_BLUE << Passaro[i][j] << ANSI_COLOR_RESET;
       	 }
        }
    }
	}
	if(cor=='3'){
	for (i = 0; i < 2; i++) {
        printf("\n");
        for (j = 0; j < 6; j++) {
			gotoxy(j + 2, i + PassaroPos);	
            if (((i==1) && (j==5)) || ((i==1) && (j==2))) {
            	cout << ANSI_COLOR_YELLOW << Passaro[i][j] << ANSI_COLOR_RESET;
			} else if ((i==0) && (j==3)) {
				cout << ANSI_COLOR_RESET << Passaro[i][j] << ANSI_COLOR_RESET;	
			} else {
            cout << ANSI_COLOR_PURPLE << Passaro[i][j] << ANSI_COLOR_RESET;
       	 }
        }
    }
	}
}
//----------------------------------------------------
void ApagaPassaro() {
    for (int i = 0; i < 2; i++) {
        for (int j = 0; j < 6; j++) {
            gotoxy(j + 2, i + PassaroPos);
            printf(" ");
        }
    }
}
//----------------------------------------------------
int Colisao(){
	if( CanoPos[0] >= 61  ){
		if( PassaroPos<buracoPos[0] || PassaroPos >buracoPos[0]+buraco_tam ){
			return 1; //morreu
		}
	}
	return 0;
}
//----------------------------------------------------
void gameover(){
	system("cls");
	printf("__________________________________________________________________\n");
	printf("\n  dP““b8    db    8b    d8 888888  dP“Yb  Yb    dP 888888 88““Yb ");
	printf("\n dP   `“   dPYb   88b  d88 88__   dP   Yb  Yb  dP  88__   88__dP ");
	printf("\n Yb  “88  dP__Yb  88YbdP88 88““   Yb   dP   YbdP   88““   88“Yb  ");
	printf("\n  YboodP dP““““Yb 88 YY 88 888888  YbodP     YP    888888 88  Yb ");
	printf("\n__________________________________________________________________");
	printf("\n\n         [Precione qualquer tecla para voltar ao menu]         ");
	getch();
}
//----------------------------------------------------
void updateScore(){
	gotoxy(jan_larg + 6, 5);cout<<"Placar: "<<score<<endl;
	gotoxy(jan_larg + 6, 7);printf("Record: %d",record);
}
//----------------------------------------------------
void instrucao(){	
	system("cls");
	printf("________________________________________________________________________");
	printf("\n                                                                        |");
	printf("\n                       ##                                               |");
	printf("\n ####                  ##                                               |");
	printf("\n  ##                   ##                                               |");
	printf("\n  ##    # ##    ####  ####   # ###  ## ##   ####   ###    ###    ####   |");
	printf("\n  ##    ## ##  ##      ##    ###    ## ##  ##     ## ##  ## ##  ##      |");
	printf("\n  ##    ## ##   ###    ##    ##     ## ##  ##     ## ##  #####   ###    |");
	printf("\n  ##    ## ##     ##   ##    ##     ## ##  ##     ## ##  ##        ##   |");
	printf("\n ####   ## ##  ####     ##   ##      ## #   ####   ###    ###   ####    |");
	printf("\n________________________________________________________________________|");
	printf("\n                      Pressione ESPAÇO para pular                       |");
	printf("\n             Pressione 1, 2 e 3 para mudar a cor do pássaro             |");
	printf("\n________________________________________________________________________|");
	printf("\n\n[Aperte qualquer tecla para voltar ao menu]");
	getch();
}
//----------------------------------------------------
void play(){
	
	PassaroPos = 6; //nasce na posição 6
	score = 0; //placar
	CanoFlag[0] = 1; 
	CanoFlag[1] = 0;
	CanoPos[0] = CanoPos[1] = 4;
	
	system("cls"); 
	DesBorder();
	genCano(0); //passagem no Cano
	updateScore();//gerando e somando o placar
	
	gotoxy(jan_larg + 5, 2);cout<<"FLAPPY BIRD";
	gotoxy(jan_larg + 6, 4);cout<<"----------";
	gotoxy(jan_larg + 6, 6);cout<<"----------";
	gotoxy(jan_larg + 6, 8);cout<<"----------";
	gotoxy(jan_larg + 6, 12);cout<<"Controles";
	gotoxy(jan_larg + 6, 13);cout<<"---------";
	gotoxy(jan_larg + 2, 14);cout<<"  Espaço = pular";
	
	gotoxy(12, 5);cout<<"Precione qualquer tecla para iniciar o jogo";
	getch();
	gotoxy(12, 5);cout<<"                                           ";
	
	int velocidade,l;
	
	while(1){
		if(score>record){
			record=score;
		}
		char ch;
		if (score < 50){
			pulo = 4;
		} else {
			pulo = 5;
		}
		if(kbhit()){
			ch = getch();
			if(ch==32){
				if( PassaroPos > pulo )
					PassaroPos-=pulo;
			} 
			//personalização no meio do jogo
			if(ch==49){
				(cor='1');
			} 
			if(ch==50){
				(cor='2');
			} 
			if(ch==51){
				(cor='3');
			} 
			//sair game
			if(ch==27){
				break;
			}
		}
		
		DesPassaro();
		DesCano(0);
		DesCano(1);
		if( Colisao() == 1 ){
			gameover();
			return;
		}
		velocidade=100;
		l=velocidade-(score*1.3);
		if (score >=60){
			l=30;
		}
		Sleep(l);
		ApagaPassaro();
		ApagaCano(0);
		ApagaCano(1);
		PassaroPos += 1;
		
		if( PassaroPos > tela_alt - 2 ){
			gameover();
			return;
		}
		
		if( CanoFlag[0] == 1 )
			CanoPos[0] += 2;
		
		if( CanoFlag[1] == 1 )
			CanoPos[1] += 2;
		
		if( CanoPos[0] >= 40 && CanoPos[0] < 42 ){
			CanoFlag[1] = 1;
			CanoPos[1] = 4;
			genCano(1);
		}
		if( CanoPos[0] > 68 ){
			score++;
			updateScore();
			CanoFlag[1] = 0;
			CanoPos[0] = CanoPos[1];
			buracoPos[0] = buracoPos[1];
		}
		
	}
	 
}
//----------------------------------------------------
void personalizar(){
	while (1) {
		system("cls");
		printf("-------------------------\n");
		printf("Qual cor deseja colocar?\n");
		int i, j;
		printf("Opção 1:\n"); //Amarelo(padrão)
	    for (i = 0; i < 2; i++) {
	        printf("\n");
	        for (j = 0; j < 6; j++) {	
	            if (((i==1) && (j==5)) || ((i==1) && (j==2))) {
	            	cout << ANSI_COLOR_RED << Passaro[i][j] << ANSI_COLOR_RESET;
				} else if ((i==0) && (j==3)) {
					cout << ANSI_COLOR_RESET << Passaro[i][j] << ANSI_COLOR_RESET;	
				} else {
	            cout << ANSI_COLOR_YELLOW << Passaro[i][j] << ANSI_COLOR_RESET;
	       	 }
	        }
	    }
	    printf("\n\nOpção 2:\n"); //Azul
		for (i = 0; i < 2; i++) {
	        printf("\n");
	        for (j = 0; j < 6; j++) {	
	            if (((i==1) && (j==5)) || ((i==1) && (j==2))) {
	            	cout << ANSI_COLOR_ORANGE << Passaro[i][j] << ANSI_COLOR_RESET;
				} else if ((i==0) && (j==3)) {
					cout << ANSI_COLOR_RESET << Passaro[i][j] << ANSI_COLOR_RESET;	
				} else {
	            cout << ANSI_COLOR_BLUE << Passaro[i][j] << ANSI_COLOR_RESET;
	       	 }
	        }
	    }
	    printf("\n\nOpção 3:\n"); //Roxo
		for (i = 0; i < 2; i++) {
	        printf("\n");
	        for (j = 0; j < 6; j++) {	
	            if (((i==1) && (j==5)) || ((i==1) && (j==2))) {
	            	cout << ANSI_COLOR_YELLOW << Passaro[i][j] << ANSI_COLOR_RESET;
				} else if ((i==0) && (j==3)) {
					cout << ANSI_COLOR_RESET << Passaro[i][j] << ANSI_COLOR_RESET;	
				} else {
	            cout << ANSI_COLOR_PURPLE << Passaro[i][j] << ANSI_COLOR_RESET;
	       	    }	
	        }
	    }
	    printf("\n-------------------------\n");
	    printf("Digite qual a opção: ");
		cor = getche();
		if ((cor > '0') && (cor < '4')){
			break;
		}
	}
}
//----------------------------------------------------
void pause(){
	int g;
	for (g=0;g<=100000000;g++){
	}
}
//----------------------------------------------------
void creditos (){
	int m,lim;
	lim=1;
	for(m=21;m>=lim;m--){
		if(m>20){
			printf("");
		} else {
			system("cls");
			gotoxy(1,m); printf("Desenvovdores:");
			gotoxy(1,m+1); printf("André Gustavo 23227356-2");
			gotoxy(1,m+2); printf("Isaac Arantes 23106516-2");
			gotoxy(1,m+3); printf("João Victor 23019052-2");
		}
		pause();
	}
}
//----------------------------------------------------
int main(){
	setlocale(LC_ALL,"Portuguese");
	setcursor(0,0); 
	srand((unsigned)time(NULL));
	
	do{
		system("cls");
		gotoxy(10,1); printf(".---. .-.                             .---.  _         .-.");
		gotoxy(10,2); printf(": .--': :                             : .; ::_;        : :");
		gotoxy(10,3); printf(": `;  : :   .--.  .---. .---. .-..-.  :   .'.-..--.  .-' :");
		gotoxy(10,4); printf(": :   : :_ ' .; ; : .; `: .; `: :; :  : .; :: :: ..'' .; :");
		gotoxy(10,5); printf(":_;   `.__;`.__,_;: ._.': ._.'`._. ;  :___.':_;:_;  `.__.'");
		gotoxy(10,6); printf("                  : :   : :    .-. :                      ");
		gotoxy(10,7); printf("                  :_;   :_;    `._.'                     ");
		gotoxy(14,9); printf("+------------------------------------------------+");
		gotoxy(14,10); printf("¦                                                ¦");
		gotoxy(14,11); printf("¦       1 - Começar jogo                         ¦"); 
		gotoxy(14,12); printf("¦       2 - Instruções                           ¦");
		gotoxy(14,13); printf("¦       3 - Personalizar                         ¦");
		gotoxy(14,14); printf("¦       4 - Sair                                 ¦");
		gotoxy(14,15); printf("¦       5 - Créditos                             ¦");		
		gotoxy(14,16); printf("¦                                                ¦");
		gotoxy(14,17); printf("+------------------------------------------------+");
		gotoxy(14,18); printf("Selecione a opção: ");
		char op = getche();
	
		if(op=='1') play();
		else if(op=='2') instrucao();
		else if(op=='3') personalizar();
		else if(op=='4') exit(0);
		else if(op=='5') creditos();
		
	}while(1);
	
	return 0;
} 
