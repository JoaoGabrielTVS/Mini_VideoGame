#define F_CPU 16000000UL
#include <stdint.h>
#include <avr/io.h>
#include <util/delay.h>
#include <avr/interrupt.h>
#define maximo_tamanho 64
#define FRAMES 35
#define velocidade_da_bola 5
#define SEM_DIRECAO  0
#define DIRECAO_PRA_BAIXO  1
#define DIRECAO_PARA_CIMA    2
#define DIRECAO_ESQUERDA  3
#define DIRECAO_DIREITA 4

volatile uint8_t comando = SEM_DIRECAO;
int8_t var_escolha = 0;

int cobra_x[maximo_tamanho] = {0};
int cobra_y[maximo_tamanho] = {0};
int8_t tam_cobra = 1;

int maca_x = 2;
int maca_y = 2;

int tick;
///////////////////////////////////////////////////////////////////////////////////////////
// COBRA
uint8_t estados[8] = {
	0b11111110, 0b11111101, 0b11111011, 0b11110111,
	0b11101111, 0b11011111, 0b10111111, 0b01111111
};

void colocar_maca(void){
	for (int y = 0; y < 8; y++){
		for (int x = 0; x < 8; x++){
			uint8_t ocup = 0;
			for (int i = 0; i < tam_cobra; i++){
				if (cobra_x[i] == x && cobra_y[i] == y) { ocup = 1; break; }
			}
			if (!ocup){
				maca_x = x;
				maca_y = y;
				return;
			}
		}
	}
}

void move_cobra(int nx, int ny){//logica para mover cobra/
	if (nx < 0) nx = 7;
	if (nx > 7) nx = 0;
	if (ny < 0) ny = 7;
	if (ny > 7) ny = 0;

	int novo_tamanho = tam_cobra;
	uint8_t ate = 0;
	if (nx == maca_x && ny == maca_y){
		if (tam_cobra < maximo_tamanho) {
			novo_tamanho = tam_cobra + 1;
			ate = 1;
		}
	}

	for (int i = novo_tamanho - 1; i > 0; i--){
		cobra_x[i] = cobra_x[i-1];
		cobra_y[i] = cobra_y[i-1];
	}

	cobra_x[0] = nx;
	cobra_y[0] = ny;

	if (ate) {
		tam_cobra = novo_tamanho;
		colocar_maca();
	}

	for (int i = 1; i < tam_cobra; i++){
		if (cobra_x[i] == cobra_x[0] && cobra_y[i] == cobra_y[0]) {
			// PERDEU AQUIK
			while (1) {
				PORTD = 0xFF;
				PORTB = 0xFF;
				_delay_ms(200);
				PORTD = 0b00011000;
				PORTB = 0b11100111;
				_delay_ms(200);
				PORTD = 0b01111110;
				PORTB = 0b10000001;
				_delay_ms(200);
				PORTD = 0b00011000;
				PORTB = 0b11100111;
				_delay_ms(200);
				var_escolha = 0;
				main();
			}
		}
	}
}

void printar_tela_jogo(){
	for (int y = 0; y < 8; y++) {
		uint8_t mascara_coluna = 0x00;
		for (int i = 0; i < tam_cobra; i++){
			if (cobra_y[i] == y){
				mascara_coluna |= (uint8_t)(~estados[cobra_x[i]]);
			}
		}

		if (maca_y == y){
			mascara_coluna |= (uint8_t)(~estados[maca_x]);
		}

		PORTD = mascara_coluna;
		PORTB = ~estados[y];

		_delay_us(1000);
	}

	PORTB = 0x00;
}

void mover_direita()
{
	while (comando == DIRECAO_DIREITA) {
		int nx = cobra_x[0] + 1;
		int ny = cobra_y[0];
		move_cobra(nx, ny);
		for (int k = 0; k < tick; k++) printar_tela_jogo();
	}
}

void mover_esquerda(void)
{
	while (comando == DIRECAO_ESQUERDA) {
		int nx = cobra_x[0] - 1;
		int ny = cobra_y[0];
		move_cobra(nx, ny);
		for (int k = 0; k < tick; k++) printar_tela_jogo();
	}
}

void mover_cima(void) {
	while (comando == DIRECAO_PARA_CIMA) {
		int nx = cobra_x[0];
		int ny = cobra_y[0] - 1;
		move_cobra(nx, ny);
		for (int k = 0; k < tick; k++) printar_tela_jogo();
	}
}

void mover_baixo(void) {
	while (comando == DIRECAO_PRA_BAIXO) {
		int nx = cobra_x[0];
		int ny = cobra_y[0] + 1;
		move_cobra(nx, ny);
		for (int k = 0; k < tick; k++) printar_tela_jogo();
	}
}
///////////////////////////////////////////////////////////////////////////////////////////
// PONG

volatile uint8_t framebuffer[8];
volatile uint8_t line_idx = 0;

uint8_t pontos_esquerda = 0;   
uint8_t pontos_direita  = 0;  
volatile uint16_t timer_vitoria = 0; 
volatile uint8_t em_vitoria = 0;    
uint8_t vencedor = 0;              


int8_t derrotas_raquete_esquerda = 0;
int8_t derrotas_raquete_direita = 0;

int requete_1_esquerda_y = 2;
int raquete_1_direita_y = 2;
const int tamanho_raquete = 4;

int bola_x = 3;
int bola_y = 3;
int angulador_bola_x = 1;
int angulador_bola_y = 1;

volatile int8_t movimento_esquerda = 0;
volatile int8_t movimento_direita = 0;

volatile int8_t left_req = 0;
volatile int8_t right_req = 0;

uint8_t ball_timer = 0;

volatile uint8_t prev_pinc = 0x0F;

void imprimidor_tela(void){
	for (int y = 0; y < 8; y++){
		uint8_t colmask = 0x00;
		for (int k = 0; k < tamanho_raquete; k++){
			int py = requete_1_esquerda_y + k;
			if (py >= 0 && py < 8 && py == y){
				colmask |= (uint8_t)(~estados[0]);
			}
		}

		for (int k = 0; k < tamanho_raquete; k++){
			int py = raquete_1_direita_y + k;
			if (py >= 0 && py < 8 && py == y){
				colmask |= (uint8_t)(~estados[7]);
			}
		}

		if (bola_y == y){
			colmask |= (uint8_t)(~estados[bola_x]);
		}

		PORTD = colmask;
		PORTB = ~estados[y];
		_delay_us(600);
	}

	PORTB = 0x00;
}

void atualizador_de_raquete(void){
	if (left_req < 0) {
		if (requete_1_esquerda_y > 0) requete_1_esquerda_y--;
		left_req = 0;
	} else if (left_req > 0) {
		if (requete_1_esquerda_y + tamanho_raquete < 8) requete_1_esquerda_y++;
		left_req = 0;
	}

	if (right_req < 0) {
		if (raquete_1_direita_y > 0) raquete_1_direita_y--;
		right_req = 0;
	} else if (right_req > 0) {
		if (raquete_1_direita_y + tamanho_raquete < 8) raquete_1_direita_y++;
		right_req = 0;
	}
}

void game_over_display(void){
	while (1) {
		if(derrotas_raquete_direita == 3){
			PORTD = ~(0b00000001);
			PORTB = ~(0b11000011);
			_delay_ms(200);
		}
		else{
			PORTD = ~(0b10000000);
			PORTB = ~(0b11000011);
			_delay_ms(200);
		}
	}
}


void atualizador_bola(void){
	int nx = bola_x + angulador_bola_x;
	int ny = bola_y + angulador_bola_y;

	if (ny < 0) { ny = 0; angulador_bola_y = -angulador_bola_y; }
	if (ny > 7) { ny = 7; angulador_bola_y = -angulador_bola_y; }

	if (nx < 0){
		if ( (ny >= requete_1_esquerda_y) && (ny < requete_1_esquerda_y + tamanho_raquete) ) {
			angulador_bola_x = -angulador_bola_x;
			nx = bola_x + angulador_bola_x;
		} else {
			pontos_direita += 1;
			derrotas_raquete_esquerda += 1; 
			if (pontos_direita >= 3) {
				em_vitoria = 1;
				vencedor = 2; 
				timer_vitoria = 0;
			}

			bola_x = 3; bola_y = 3; angulador_bola_x = 1; angulador_bola_y = 1;
			return;
		}
	}

	if (nx > 7){
		if ( (ny >= raquete_1_direita_y) && (ny < raquete_1_direita_y + tamanho_raquete) ) {
			angulador_bola_x = -angulador_bola_x;
			nx = bola_x + angulador_bola_x;
		} else {
			pontos_esquerda += 1;
			derrotas_raquete_direita += 1;

			if (pontos_esquerda >= 3) {
				em_vitoria = 1;
				timer_vitoria = 0;
			}

			bola_x = 3; bola_y = 3; angulador_bola_x = -1; angulador_bola_y = 1;
			return;
		}
	}

	bola_x = nx;
	bola_y = ny;
}

/* PCINT: mantive seu código (mínimas mudanças) */
ISR(PCINT1_vect){
	uint8_t s = PINC & 0x0F;
	if(var_escolha == 1){
		uint8_t s = PINC & 0x0F;
		if ( (prev_pinc & 0x01) && !(s & 0x01) ) {
			left_req = 1;
		}
		if ( (prev_pinc & 0x02) && !(s & 0x02) ) {
			left_req = -1;
		}
		if ( (prev_pinc & 0x04) && !(s & 0x04) ) {
			right_req = -1;
		}
		if ( (prev_pinc & 0x08) && !(s & 0x08) ) {
			right_req = 1;
		}

		if (!(s & 0b00000001)) movimento_esquerda = -1;
		else if (!(s & 0b00000010)) movimento_esquerda = 1;
		else movimento_esquerda = 0;

		if (!(s & 0b00000100)) movimento_direita = -1;
		else if (!(s & 0b00001000)) movimento_direita = 1;
		else movimento_direita = 0;

		prev_pinc = s;
	}
	else if(var_escolha == 2){
		uint8_t s = PINC & 0x0F;
		if (!(s & (1 << PC0))) { comando = DIRECAO_PRA_BAIXO;  return; }
		if (!(s & (1 << PC1))) { comando = DIRECAO_PARA_CIMA;    return; }
		if (!(s & (1 << PC2))) { comando = DIRECAO_ESQUERDA;  return; }
		if (!(s & (1 << PC3))) { comando = DIRECAO_DIREITA; return; }
	}else{
		if(!(s & (1 << PC0))){
			var_escolha = 1;
		}
		else{
			var_escolha = 2;
		}
	}
}

ISR(TIMER0_COMPA_vect) {
	if (em_vitoria) {
		timer_vitoria++; 
	}
}

int main(void){
	DDRD = 0xFF;
	DDRB = 0xFF;
	DDRC = 0b00000000;

	PORTC |= 0b00001111;

	PORTB = 0x00;
	PORTD = 0x00;
	prev_pinc = PINC & 0x0F;

	TCCR0A = (1<<WGM01);               
	TCCR0B = (1<<CS01) | (1<<CS00);    
	OCR0A = 249;                       
	TIMSK0 |= (1<<OCIE0A);             

	PCICR  = 0b00000010;
	PCMSK1 = 0b00001111;
	sei();

	while(1){
		PORTD = ~0b00000100;
		PORTB = 0b000111110;
		_delay_ms(10);
		PORTD = ~0b00001000;
		PORTB = 0b000011100;
		_delay_ms(10);
		PORTD = ~0b00010000;
		PORTB = 0b000011100;
		_delay_ms(10);
		PORTD = ~0b00100000;
		PORTB = 0b000001000;
		_delay_ms(10);

		if(var_escolha == 1){
			tick = FRAMES;
			while (1){
				for (int rep=0; rep<8; rep++){
					atualizador_de_raquete();
					imprimidor_tela();
				}

				if (em_vitoria) {
					while (timer_vitoria < 3000) {
						if (vencedor == 1) {
							PORTD = ~(0b00000001);
							PORTB = ~(0b11000011);
						} else {
							PORTD = ~(0b10000000);
							PORTB = ~(0b11000011);
						}
						_delay_ms(200);
						for (int y=0;y<8;y++) framebuffer[y] = 0xFF; // tudo apagado
						
						imprimidor_tela();
						var_escolha=0;
					}
					pontos_esquerda = 0;
					pontos_direita = 0;
					derrotas_raquete_esquerda = 0;
					derrotas_raquete_direita = 0;
					em_vitoria = 0;
					timer_vitoria = 0;
					vencedor = 0;
					bola_x = 3; bola_y = 3; 
					angulador_bola_x = 1;
					angulador_bola_y = 1;
					requete_1_esquerda_y = 2; 
					raquete_1_direita_y = 2;
					var_escolha = 0;  
					 break;
				}

				ball_timer++;
				if (ball_timer >= velocidade_da_bola) {
					ball_timer = 0;
					atualizador_bola();
				}

				for (int k=0;k<tick;k++) imprimidor_tela();
			}
		}
		else if(var_escolha == 2){
			tick = 64;
			PORTB = 0x00;
			PORTD = 0xFF;
			for (int i = 0; i < maximo_tamanho; i++){ cobra_x[i] = 0; cobra_y[i] = 0; }
			tam_cobra = 1;
			cobra_x[0] = 0;
			cobra_y[0] = 0;
			while(1){
				if (comando == DIRECAO_DIREITA) {
					mover_direita();
				} else if (comando == DIRECAO_ESQUERDA) {
					mover_esquerda();
				} else if (comando == DIRECAO_PARA_CIMA) {
					mover_cima();
				} else if (comando == DIRECAO_PRA_BAIXO) {
					mover_baixo();
				} else {
					for (int k = 0; k < 8; k++) printar_tela_jogo();
					_delay_ms(10);
				}
			}
		}
	}

	return 0;
}
