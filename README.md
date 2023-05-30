# trabalho-AV2-programa-o-em-C
Não consegui enviar o arquivo zip então compartilhei o trabalho que esta aqui.
/* Trabalho de Software Básico para AV2 Feito */
/* Nomes: Augusto Pubell; Nathan de Medeiros; Maria Eduarda */


#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/socket.h>
#include <netinet/in.h>

#define PORT 7777
#define MAX_JOKES 10

/* compilar o cÃ³digo com a biblioteca gcc: gcc server.c -o server */ 
/* execute o servidor com: ./server */ 

char* jokes[MAX_JOKES] = {
    "Por que o cachorro entrou na igreja? Para buscar o osso santo!",
    "Qual Ã© o contrÃ¡rio de volÃ¡til? Vem cÃ¡ sobrinho!",
    "O que o padeiro falou para o pÃ£o? Nossa, vocÃª tÃ¡ uma fatia!",
    "Por que o gato foi ao dentista? Porque tinha cÃ¡rie-miau!",
    "Como chamamos um boomerang que nÃ£o volta? Um pedaÃ§o de madeira.",
    "Qual Ã© o animal mais antigo do mundo? A zebra, porque estÃ¡ sempre a preto e branco.",
    "Por que a Coca-Cola e a Fanta se dÃ£o muito bem? Porque se a Fanta quebra, a Coca-Cola!",
    "O que o estudante de medicina foi fazer na horta? Plantar 'raldiologia'.",
    "Por que o livro foi no mÃ©dico? Porque estava cheio de histÃ³rias mal contadas!",
    "Por que a matemÃ¡tica Ã© tÃ£o traÃ­ra? Porque ela tem problemas com todos os 'x' que jÃ¡ pegou."
};

int main() {
    int server_fd, new_socket;
    struct sockaddr_in address;
    int addrlen = sizeof(address);

    // CriaÃ§Ã£o do socket TCP
    if ((server_fd = socket(AF_INET, SOCK_STREAM, 0)) == 0) {
        perror("Erro na criaÃ§Ã£o do socket");
        exit(EXIT_FAILURE);
    }

    address.sin_family = AF_INET;
    address.sin_addr.s_addr = INADDR_ANY;
    address.sin_port = htons(PORT);

    // Associa o socket a um endereÃ§o e porta
    if (bind(server_fd, (struct sockaddr *)&address, sizeof(address)) < 0) {
        perror("Erro no bind");
        exit(EXIT_FAILURE);
    }

    // Aguarda conexÃµes
    if (listen(server_fd, 3) < 0) {
        perror("Erro no listen");
        exit(EXIT_FAILURE);
    }

    printf("Servidor TCP aguardando conexÃµes na porta %d...\n", PORT);

    while (1) {
        // Aceita uma nova conexÃ£o
        if ((new_socket = accept(server_fd, (struct sockaddr *)&address, (socklen_t*)&addrlen)) < 0) {
            perror("Erro no accept");
            exit(EXIT_FAILURE);
        }

        // Gera um Ã­ndice aleatÃ³rio para selecionar uma piada
        int random_index = rand() % MAX_JOKES;
        char* joke = jokes[random_index];

        // Envia a piada para o cliente
        send(new_socket, joke, strlen(joke), 0);
        printf("Piada enviada: %s\n", joke);

        close(new_socket);
    }

    return 0;
}
