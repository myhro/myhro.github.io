---
date: 2011-06-17 19:07:17
layout: post
slug: modularizacao-de-programas-em-c
title: Modularização de programas em C
categories:
- c
- programacao
---

Ainda não sei porque não se ensinam em cursos de tecnologia coisas tão básicas quanto modularização de programas ou controle de versões. É de se esperar que no ensino superior não se receba tudo mastigado, mas duas técnicas tão simples e úteis não deveriam simplesmente passar batidas. A distribuição de funções em arquivos separados (headers, os famosos ".h") facilita não só a manutenção e leitura do código, como a sua capacidade de reutilização (um dos propósitos primordiais das mesmas) e até mesmo velocidade de compilação (algo crucial em grandes projetos).

Além disso, a obrigatoriedade de se criar projetos no CodeBlocks (dica: nunca utilize o DevCpp) para isto nos traz outra funcionalidade extremamente útil: a possibilidade de debugá-lo. Quando se compila um código-fonte, o compilador apenas acusará erros de sintaxe que impeçam a compilação correta do programa. Ao se optar pelo uso do debugger, pequenos detalhes como uma variável declarada mas não utilizada ou funções chamadas sem terem sido prototipadas (ou incluídas num #include) são mostrados.

Vou exemplificar a modularização de uma lista encadeada onde hipoteticamente se cadastra uma pessoa e um bilhete. Aqui temos o código em um arquivo só:

``` c main.c
#include <conio.h>
#include <stdio.h>
#include <stdlib.h>

typedef struct celula *Apontador;

typedef struct {
	int num;
	char nome[21];
} Bilhete;

typedef struct celula {
	Bilhete Item;
	Apontador Prox;
} Celula;

typedef struct {
	Apontador Primeiro;
	Apontador Ultimo;
} TipoLista;

int testa(TipoLista Lista);

void imprime(TipoLista Lista) {
	Apontador aux;
	int i = 1;

	if (testa(Lista) == 1) {
		printf("\nLista vazia.\n");
		return;
	}

	aux = Lista.Primeiro->Prox;

	while (aux != NULL) {
		printf("\n%d. Nome: %s. Bilhete: %d", i, aux->Item.nome, aux->Item.num);
		aux = aux->Prox;
		i++;
	}

	printf("\n");
}

void inicializa(TipoLista *Lista) {
	Lista->Primeiro = (Apontador) malloc(sizeof(Celula));
	Lista->Primeiro->Prox = NULL;
	Lista->Ultimo = Lista->Primeiro;
}

void insere(TipoLista *Lista, Bilhete X) {
	Lista->Ultimo->Prox = (Apontador) malloc(sizeof(Celula));
	Lista->Ultimo = Lista->Ultimo->Prox;
	Lista->Ultimo->Prox = NULL;
	Lista->Ultimo->Item = X;
}

void libera(TipoLista *Lista) {
    Apontador aux;

    if (testa(*Lista) == 1) {
		printf("\nLista vazia.\n");
		return;
	}

	aux = Lista->Primeiro;
	while (aux != NULL) {
	    Lista->Primeiro = aux->Prox;
	    free(aux);
	    aux = Lista->Primeiro;
	}

	printf("\nLiberacao ok.\n");
}

void libera_cabeca(TipoLista *Lista) {
    free(Lista->Primeiro);
}

void retira(TipoLista *Lista, int n) {
	Apontador aux, rem, tmp;
	int removido = 0;

	if (testa(*Lista) == 1) {
		printf("\nLista vazia.\n");
		return;
	}

	aux = Lista->Primeiro;

	while (aux != NULL) {
		if (aux->Item.num == n) {
			rem = aux;
			tmp->Prox = aux->Prox;
			if (aux->Prox == NULL) {
			    Lista->Ultimo = tmp;
			}
			free(rem);
			printf("\nBilhete %d removido.\n", n);
			removido = 1;
			return;
		}
		tmp = aux;
		aux = aux->Prox;
	}

	if (removido == 0) {
	    printf("\nBilhete inexistente.\n");
	}
}

int testa(TipoLista Lista) {
	return(Lista.Primeiro == Lista.Ultimo);
}

int main() {
	TipoLista loteria;
	Bilhete apostador;
	int op, rem;

	inicializa(&loteria);

	while(1) {
	    op = 0;
	    system("cls");
	    printf("Menu: \n\n1. Adicionar\n2. Remover\n3. Listar\n4. Liberar\n5. Sair\n\nOpcao: ");
	    fflush(stdin);
	    scanf("%d", &op);
	    if (op == 1) {
	        printf("\nBilhete: ");
            fflush(stdin);
            scanf("%d", &apostador.num);
            printf("Nome: ");
            fflush(stdin);
            scanf("%20[^\n]", apostador.nome);
            insere(&loteria,apostador);
            continue;
	    }
	    if (op == 2) {
	        if (testa(loteria) == 1) {
                printf("\nLista vazia.\n");
                getch();
                continue;
            }
	        imprime(loteria);
	        printf("\nQual bilhete deseja retirar? ");
	        fflush(stdin);
            scanf("%d", &rem);
            retira(&loteria,rem);
            getch();
            continue;
	    }
	    if (op == 3) {
	        imprime(loteria);
	        getch();
	        continue;
	    }
	    if (op == 4) {
	        libera(&loteria);
	        inicializa(&loteria);
	        getch();
	        continue;
	    }
	    if (op == 5) {
	        break;
	    }
	    else {
	        printf("\nOpcao invalida.");
	        getch();
	        continue;
	    }
	}

    if (testa(loteria) != 1) {
        libera(&loteria);
        libera_cabeca(&loteria);
    }
    else {
        libera_cabeca(&loteria);
    }

	return 0;
}
```

E aqui temos o mesmo modularizado:

``` c main.c
#include <conio.h>
#include <stdio.h>
#include "lista.h"

int main() {
	TipoLista loteria;
	Bilhete apostador;
	int op, rem;

	inicializa(&loteria);

	while(1) {
	    op = 0;
	    system("cls");
	    printf("Menu: \n\n1. Adicionar\n2. Remover\n3. Listar\n4. Liberar\n5. Sair\n\nOpcao: ");
	    fflush(stdin);
	    scanf("%d", &op);
	    if (op == 1) {
	        printf("\nBilhete: ");
            fflush(stdin);
            scanf("%d", &apostador.num);
            printf("Nome: ");
            fflush(stdin);
            scanf("%20[^\n]", apostador.nome);
            insere(&loteria,apostador);
            continue;
	    }
	    if (op == 2) {
	        if (testa(loteria) == 1) {
                printf("\nLista vazia.\n");
                getch();
                continue;
            }
	        imprime(loteria);
	        printf("\nQual bilhete deseja retirar? ");
	        fflush(stdin);
            scanf("%d", &rem);
            retira(&loteria,rem);
            getch();
            continue;
	    }
	    if (op == 3) {
	        imprime(loteria);
	        getch();
	        continue;
	    }
	    if (op == 4) {
	        libera(&loteria);
	        inicializa(&loteria);
	        getch();
	        continue;
	    }
	    if (op == 5) {
	        break;
	    }
	    else {
	        printf("\nOpcao invalida.");
	        getch();
	        continue;
	    }
	}

    if (testa(loteria) != 1) {
        libera(&loteria);
        libera_cabeca(&loteria);
    }
    else {
        libera_cabeca(&loteria);
    }

	return 0;
}
```

``` c lista.h
#include <stdio.h>
#include <stdlib.h>

typedef struct celula *Apontador;

typedef struct {
	int num;
	char nome[21];
} Bilhete;

typedef struct celula {
	Bilhete Item;
	Apontador Prox;
} Celula;

typedef struct {
	Apontador Primeiro;
	Apontador Ultimo;
} TipoLista;

void imprime(TipoLista Lista);

void inicializa(TipoLista *Lista);

void insere(TipoLista *Lista, Bilhete X);

void libera(TipoLista *Lista);

void libera_cabeca(TipoLista *Lista);

void retira(TipoLista *Lista, int n);

int testa(TipoLista Lista);
```

``` c lista.c
#include "lista.h"

void imprime(TipoLista Lista) {
	Apontador aux;
	int i = 1;

	if (testa(Lista) == 1) {
		printf("\nLista vazia.\n");
		return;
	}

	aux = Lista.Primeiro->Prox;

	while (aux != NULL) {
		printf("\n%d. Nome: %s. Bilhete: %d", i, aux->Item.nome, aux->Item.num);
		aux = aux->Prox;
		i++;
	}

	printf("\n");
}

void inicializa(TipoLista *Lista) {
	Lista->Primeiro = (Apontador) malloc(sizeof(Celula));
	Lista->Primeiro->Prox = NULL;
	Lista->Ultimo = Lista->Primeiro;
}

void insere(TipoLista *Lista, Bilhete X) {
	Lista->Ultimo->Prox = (Apontador) malloc(sizeof(Celula));
	Lista->Ultimo = Lista->Ultimo->Prox;
	Lista->Ultimo->Prox = NULL;
	Lista->Ultimo->Item = X;
}

void libera(TipoLista *Lista) {
    Apontador aux;

    if (testa(*Lista) == 1) {
		printf("\nLista vazia.\n");
		return;
	}

	aux = Lista->Primeiro;
	while (aux != NULL) {
	    Lista->Primeiro = aux->Prox;
	    free(aux);
	    aux = Lista->Primeiro;
	}

	printf("\nLiberacao ok.\n");
}

void libera_cabeca(TipoLista *Lista) {
    free(Lista->Primeiro);
}

void retira(TipoLista *Lista, int n) {
	Apontador aux, rem, tmp;
	int removido = 0;

	if (testa(*Lista) == 1) {
		printf("\nLista vazia.\n");
		return;
	}

	aux = Lista->Primeiro;

	while (aux != NULL) {
		if (aux->Item.num == n) {
			rem = aux;
			tmp->Prox = aux->Prox;
			if (aux->Prox == NULL) {
			    Lista->Ultimo = tmp;
			}
			free(rem);
			printf("\nBilhete %d removido.\n", n);
			removido = 1;
			return;
		}
		tmp = aux;
		aux = aux->Prox;
	}

	if (removido == 0) {
	    printf("\nBilhete inexistente.\n");
	}
}

int testa(TipoLista Lista) {
	return(Lista.Primeiro == Lista.Ultimo);
}
```

Resumindo:

* O header (arquivo ".h") em si contém apenas as estruturas necessárias, os protótipos das funções a serem implementadas no arquivo ".c" (ambos devem ter o mesmo nome) e as bibliotecas padrão (incluídas com os sinais "maior" e "menor").  
* O arquivo ".c", onde de fato se implementam as funções, inclui apenas o ".h" (com aspas, indicando que se trata de um arquivo localizado na mesma pasta) criado, que por sua vez incluirá as bibliotecas padrão.  
* E no arquivo principal contendo a função main() inclui-se o ".h" criado, também utilizando aspas. Na verdade não seria necessário nem mesmo incluir novamente o header **stdio.h** pois isto já foi feito no arquivo "lista.h".

Se trata de um processo bem simples que a princípio pode parecer desnecessário, mas é muito mais prático dividir seu código-fonte de 600 linhas em 2 ou 3 arquivos. Não só porque facilitará o entedimento do mesmo quando for lido por outra pessoa, como pode ajudar você a mesmo a não ficar perdido no seu próprio código.
