cmakelist

# Define a versão mínima do CMake necessária para o projeto
cmake_minimum_required(VERSION 3.27)

# Define o nome do projeto e a linguagem utilizada (C)
project(projeto04 C)

# Define o padrão da linguagem C a ser utilizada no projeto
set(CMAKE_C_STANDARD 11)

# Adiciona os arquivos fonte e cabeçalhos ao executável
add_executable(projeto04
        main.c        # Arquivo principal do programa
        cliente.c     # Implementação das funções de cliente
        cliente.h     # Cabeçalho com declarações das funções de cliente
        livro.c       # Implementação das funções de livro
        livro.h       # Cabeçalho com declarações das funções de livro
        validacao.c   # Implementação das funções de validação (CPF, ISBN, telefone)
        validacao.h   # Cabeçalho com declarações das funções de validação
)

main.c

#include <stdio.h>
#include "cliente.h"
#include "livro.h"

/**
 * @brief Função principal que exibe o menu e gerencia as operações do sistema.
 *
 * A função principal exibe um menu de opções para o usuário, permitindo que ele
 * escolha diferentes ações como cadastrar clientes, livros, emprestar livros,
 * listar clientes e livros, renovar empréstimos, devolver livros, excluir clientes
 * ou livros, ou sair do sistema.
 * A escolha do usuário é lida e a ação correspondente é executada.
 *
 * @return Retorna 0 quando o programa termina.
 */
int main() {
    int opcao; /< Variável que armazena a opção escolhida pelo usuário. */

    // Laço que mantém o menu em execução até o usuário escolher a opção de sair (opção 10).
    do {
        // Exibe o menu de opções
        printf("\nMenu:\n");
        printf("1. Cadastrar cliente\n");
        printf("2. Cadastrar livro\n");
        printf("3. Emprestar livro\n");
        printf("4. Listar clientes\n");
        printf("5. Listar livros\n");
        printf("6. Renovar emprestimo\n");
        printf("7. Devolver livro\n");
        printf("8. Excluir cliente\n");
        printf("9. Excluir livro\n");
        printf("10. Sair\n");
        printf("Escolha uma opcao: ");
        scanf("%d", &opcao); /< Lê a opção escolhida pelo usuário. */

        // Verifica a opção escolhida e chama a função correspondente
        switch(opcao) {
            case 1:
                cadastrarCliente(); /< Chama a função para cadastrar um cliente. */
                break;
            case 2:
                cadastrarLivro();   /< Chama a função para cadastrar um livro. */
                break;
            case 3:
                emprestarLivro();   /< Chama a função para emprestar um livro. */
                break;
            case 4:
                listarClientes();   /< Chama a função para listar todos os clientes cadastrados. */
                break;
            case 5:
                listarLivros();     /< Chama a função para listar todos os livros cadastrados. */
                break;
            case 6:
                renovarLivro();     /< Chama a função para renovar o empréstimo de um livro. */
                break;
            case 7:
                devolverLivro();    /< Chama a função para devolver um livro emprestado. */
                break;
            case 8:
                excluirCliente();   /< Chama a função para excluir um cliente. */
                break;
            case 9:
                excluirLivro();     /< Chama a função para excluir um livro. */
                break;
            case 10:
                printf("Saindo...\n"); /< Mensagem de saída do sistema. */
                break;
            default:
                printf("Opcao invalida!\n");
        }
    } while (opcao != 10); /< O loop continua até o usuário escolher a opção de sair. */

    return 0;              /< Retorna 0 indicando que o programa terminou com sucesso. */
}

cliente.c

#include <stdio.h>
#include <string.h>
#include "cliente.h"
#include "validacao.h"
#include "livro.h"

// Definindo a multa diária por atraso
#define MULTA_DIARIA 1.00

// Declaração de um array para armazenar os clientes e a variável que mantém o total de clientes cadastrados
Cliente clientes[MAX_CLIENTES];
int totalClientes = 0;


/**
 * @brief Função para cadastrar um novo cliente no sistema.
 *
 * Esta função verifica se há espaço para novos clientes. Se houver, solicita os dados do cliente, como nome, CPF,
 * endereço, telefone, e valida cada um desses campos. Se os dados estiverem corretos, o cliente é cadastrado e
 * adicionado ao sistema.
 */
void cadastrarCliente() {
    // Verifica se a quantidade máxima de clientes foi atingida
    if (totalClientes >= MAX_CLIENTES) {
        printf("Não ha espaco para mais clientes.\n");
        return;
    }

    Cliente novo_cliente;
    // Solicita o nome do cliente
    printf("Digite o nome completo do cliente: ");
    while(getchar() != '\n'); // Limpa o buffer do teclado
    fgets(novo_cliente.nome, 100, stdin);
    novo_cliente.nome[strcspn(novo_cliente.nome, "\n")] = '\0'; // Remove a nova linha do final do nome

    // Verifica se o nome é válido
    if (strlen(novo_cliente.nome) == 0) {
        printf("Erro: O nome e obrigatorio!\n");
        return;
    }

    // Solicita o CPF do cliente e valida
    printf("Digite o CPF do cliente (somente numeros): ");
    fgets(novo_cliente.cpf, 15, stdin);
    novo_cliente.cpf[strcspn(novo_cliente.cpf, "\n")] = '\0';

    // Validação do CPF
    if (strlen(novo_cliente.cpf) == 0 || !validarCpf(novo_cliente.cpf)) {
        printf("Erro: CPF invalido! O CPF deve conter 11 digitos numericos.\n");
        return;
    }

    // Solicita o endereço do cliente e valida
    printf("Digite o endereco do cliente: ");
    fgets(novo_cliente.endereco, 200, stdin);
    novo_cliente.endereco[strcspn(novo_cliente.endereco, "\n")] = '\0';
    if (strlen(novo_cliente.endereco) == 0) {
        printf("Erro: O endereco e obrigatorio!\n");
        return;
    }

    // Solicita o telefone do cliente e valida
    printf("Digite o telefone do cliente (somente numeros, incluindo o DDD): ");
    fgets(novo_cliente.telefone, 15, stdin);
    novo_cliente.telefone[strcspn(novo_cliente.telefone, "\n")] = '\0';
    if (strlen(novo_cliente.telefone) == 0 || !validarTelefone(novo_cliente.telefone)) {
        printf("Erro: Numero de telefone invalido! O telefone deve ter 11 digitos (incluindo o DDD).\n");
        return;
    }

    // Atribui um ID único ao cliente e inicializa os dados de empréstimo e multa
    novo_cliente.id = totalClientes + 1;
    novo_cliente.livroEmprestadoId = -1;
    novo_cliente.multa = 0.0;

    // Adiciona o cliente no array de clientes e incrementa o contador
    clientes[totalClientes] = novo_cliente;
    totalClientes++;

    // Confirma que o cliente foi cadastrado com sucesso
    printf("Cliente '%s' cadastrado com sucesso!\n", novo_cliente.nome);
}


/**
 * @brief Função para excluir um cliente cadastrado no sistema.
 *
 * Solicita o ID do cliente a ser excluído, verifica se ele existe no sistema e o remove, atualizando a lista de clientes.
 */
void excluirCliente() {
    int idCliente;

    // Solicita o ID do cliente a ser excluído
    printf("Digite o ID do cliente a ser excluido: ");
    scanf("%d", &idCliente);

    // Verifica se o cliente existe
    if (idCliente < 1 || idCliente > totalClientes) {
        printf("Cliente nao encontrado.\n");
        return;
    }

    // Remove o cliente e atualiza o array de clientes
    for (int i = idCliente - 1; i < totalClientes - 1; i++) {
        clientes[i] = clientes[i + 1];
    }

    totalClientes--; // Decrementa o contador de clientes
    printf("Cliente excluido com sucesso.\n");
}


/**
 * @brief Função para listar todos os clientes cadastrados no sistema.
 *
 * Exibe os dados de todos os clientes, incluindo o nome, CPF, endereço, telefone e informações sobre empréstimos de livros.
 */
void listarClientes() {
    // Verifica se não há clientes cadastrados
    if (totalClientes == 0) {
        printf("Nenhum cliente cadastrado.\n");
        return;
    }

    time_t agora = time(NULL);

    // Exibe a lista de clientes
    printf("Lista de clientes:\n");
    for (int i = 0; i < totalClientes; i++) {
        Cliente *cliente = &clientes[i];
        printf("ID: %d\n Nome: %s\n CPF: %s\n Endereco: %s\n Telefone: %s\n",
               cliente->id, cliente->nome, cliente->cpf, cliente->endereco, cliente->telefone);

        // Exibe informações sobre o livro emprestado, se houver
        if (cliente->livroEmprestadoId != -1) {

            struct tm *tm_info = localtime(&cliente->dataDevolucao);
            char dataDevolucao[20];
            strftime(dataDevolucao, sizeof(dataDevolucao), "%d/%m/%Y", tm_info);
            printf(" Data de devolucao prevista: %s\n", dataDevolucao);

            // Calcula a multa se o livro estiver atrasado
            if (agora > cliente->dataDevolucao) {
                cliente->multa = difftime(agora, cliente->dataDevolucao) / (60 * 60 * 24) * MULTA_DIARIA;
                printf(" Multa de atraso: R$ %.2f\n", cliente->multa);
            } else {

                printf(" Multa de atraso: R$ 0.00\n");
            }
        } else {
            printf(" Nenhum livro emprestado\n");
        }

        printf("\n");
    }
}


/**
 * @brief Função para emprestar um livro a um cliente.
 *
 * Solicita os IDs do cliente e do livro, verifica a disponibilidade do livro e, se tudo estiver correto,
 * registra o empréstimo no sistema.
 */
void emprestarLivro() {
    int idCliente, idLivro;

    // Solicita o ID do cliente
    printf("Digite o ID do cliente: ");
    scanf("%d", &idCliente);

    // Verifica se o cliente existe
    if (idCliente < 1 || idCliente > totalClientes) {
        printf("Cliente nao encontrado.\n");
        return;
    }

    Cliente *cliente = &clientes[idCliente - 1];

    // Verifica se o cliente já possui um livro emprestado
    if (cliente->livroEmprestadoId != -1) {
        printf("Este cliente ja possui um livro emprestado.\n");
        return;
    }

    // Solicita o ID do livro
    printf("Digite o ID do livro: ");
    scanf("%d", &idLivro);

    // Verifica se o livro existe e está disponível para empréstimo
    if (idLivro < 1 || idLivro > totalLivros) {
        printf("Livro nao encontrado.\n");
        return;
    }

    Livro *livro = &livros[idLivro - 1];

    if (!livro->disponivel) {
        printf("Este livro nao esta disponivel para emprestimo.\n");
        return;
    }

    // Registra o empréstimo do livro
    cliente->livroEmprestadoId = idLivro;
    cliente->dataEmprestimo = time(NULL);
    cliente->dataDevolucao = time(NULL) + (14 * 24 * 60 * 60); // 14 dias de prazo para devolução

    livro->disponivel = 0; // Marca o livro como emprestado

    // Confirma o empréstimo
    printf("Livro '%s' emprestado com sucesso ao cliente '%s'.\n", livro->titulo, cliente->nome);
}


/**
 * @brief Função para renovar o empréstimo de um livro.
 *
 * Permite ao cliente renovar o empréstimo de um livro, estendendo o prazo de devolução.
 */
void renovarLivro() {
    int idCliente;

    // Solicita o ID do cliente para renovação
    printf("Digite o ID do cliente para renovar o emprestimo: ");
    scanf("%d", &idCliente);

    // Verifica se o cliente existe
    if (idCliente < 1 || idCliente > totalClientes) {
        printf("Cliente nao encontrado.\n");
        return;
    }

    Cliente *cliente = &clientes[idCliente - 1];

    // Verifica se o cliente possui um livro emprestado
    if (cliente->livroEmprestadoId == -1) {
        printf("Este cliente nao tem livro emprestado.\n");
        return;
    }

    Livro *livro = &livros[cliente->livroEmprestadoId - 1];

    // Renova o prazo do empréstimo
    if (!livro->disponivel) {
        printf("Este livro esta emprestado, renovando emprestimo...\n");
        cliente->dataDevolucao = time(NULL) + (14 * 24 * 60 * 60); // Estende o prazo por mais 14 dias
        printf("Emprestimo renovado com sucesso para o livro '%s'.\n", livro->titulo);
    }
}


/**
 * @brief Função para devolver um livro.
 *
 * Processa a devolução do livro emprestado por um cliente, calculando a multa por atraso, se houver.
 */
void devolverLivro() {
    int idCliente;

    // Solicita o ID do cliente para devolução
    printf("Digite o ID do cliente para devolver o livro: ");
    scanf("%d", &idCliente);

    // Verifica se o cliente existe
    if (idCliente < 1 || idCliente > totalClientes) {
        printf("Cliente nao encontrado.\n");
        return;
    }

    Cliente *cliente = &clientes[idCliente - 1];

    // Verifica se o cliente possui um livro emprestado
    if (cliente->livroEmprestadoId == -1) {
        printf("Este cliente nao tem livro emprestado.\n");
        return;
    }

    Livro *livro = &livros[cliente->livroEmprestadoId - 1];


    // Verifica se há multa por atraso
    time_t agora = time(NULL);
    if (agora > cliente->dataDevolucao) {
        cliente->multa = difftime(agora, cliente->dataDevolucao) / (60 * 60 * 24) * MULTA_DIARIA;
        printf("Este livro esta atrasado. Multa gerada: R$ %.2f\n", cliente->multa);
    }


    // Marca o livro como disponível novamente e finaliza o empréstimo
    livro->disponivel = 1;
    cliente->livroEmprestadoId = -1;

    // Confirma a devolução
    printf("Livro '%s' devolvido com sucesso.\n", livro->titulo);
}

cliente.h

#ifndef PROJETO04_CLIENTE_H
#define PROJETO04_CLIENTE_H

// Inclui a biblioteca time.h para trabalhar com datas e horas
#include <time.h>

// Define o número máximo de clientes permitidos no sistema
#define MAX_CLIENTES 100

/**
 * @brief Estrutura que define as informações de um cliente.
 *
 * A estrutura Cliente armazena dados como o ID do cliente, nome, CPF, endereço, telefone,
 * o ID do livro emprestado, as datas de empréstimo e devolução, e o valor da multa de atraso.
 */
typedef struct {
    int id;                 /< Identificador único do cliente */
    char nome[100];         /< Nome completo do cliente */
    char cpf[15];           /< CPF do cliente (somente números) */
    char endereco[200];     /< Endereço do cliente */
    char telefone[15];      /< Telefone do cliente (somente números, incluindo o DDD) */
    int livroEmprestadoId;  /< ID do livro emprestado (-1 se não houver livro emprestado) */
    time_t dataEmprestimo;  /< Data e hora do empréstimo do livro */
    time_t dataDevolucao;   /< Data e hora da devolução prevista do livro */
    double multa;           /< Valor da multa caso o livro não seja devolvido no prazo */
} Cliente;

// Declarações de variáveis externas
extern Cliente clientes[MAX_CLIENTES]; /< Array de clientes cadastrados */
extern int totalClientes;              /< Total de clientes cadastrados no sistema */

// Declarações das funções relacionadas ao cliente
void cadastrarCliente();    /< Função para cadastrar um novo cliente */
void excluirCliente();      /< Função para excluir um cliente existente */
void listarClientes();      /< Função para listar todos os clientes cadastrados */
void emprestarLivro();      /< Função para emprestar um livro a um cliente */
void renovarLivro();        /< Função para renovar o empréstimo de um livro */
void devolverLivro();       /< Função para devolver um livro emprestado */


#endif //PROJETO04_CLIENTE_H

livro.c

#include <stdio.h>
#include <string.h>
#include "livro.h"
#include "cliente.h"
#include "validacao.h"

// Definindo um array para armazenar os livros e a variável que mantém o total de livros cadastrados
Livro livros[MAX_LIVROS];
int totalLivros = 0;


/**
 * @brief Função para cadastrar um novo livro no sistema.
 *
 * Esta função solicita ao usuário os dados do livro, como título, autor e ISBN, realiza a validação desses campos,
 * e se tudo estiver correto, adiciona o livro ao sistema.
 */
void cadastrarLivro() {
    // Verifica se há espaço para cadastrar mais livros
    if (totalLivros >= MAX_LIVROS) {
        printf("Nao ha espaco para mais livros.\n");
        return;
    }

    Livro novo_livro;

    // Solicita o título do livro
    printf("Digite o titulo do livro: ");
    while(getchar() != '\n');  // Limpa o buffer do teclado
    fgets(novo_livro.titulo, 100, stdin);
    novo_livro.titulo[strcspn(novo_livro.titulo, "\n")] = '\0';  // Remover o newline no final

    // Verifica se o título foi informado
    if (strlen(novo_livro.titulo) == 0) {
        printf("Erro: O titulo do livro e obrigatorio!\n");
        return;
    }

    // Solicita o autor do livro
    printf("Digite o autor do livro: ");
    fgets(novo_livro.autor, 100, stdin);
    novo_livro.autor[strcspn(novo_livro.autor, "\n")] = '\0';  // Remover o newline no final

    // Verifica se o autor foi informado
    if (strlen(novo_livro.autor) == 0) {
        printf("Erro: O autor do livro e obrigatorio!\n");
        return;
    }

    // Solicita o ISBN do livro e valida
    printf("Digite o ISBN do livro (somente numeros): ");
    fgets(novo_livro.isbn, 14, stdin);
    novo_livro.isbn[strcspn(novo_livro.isbn, "\n")] = '\0';  // Remover o newline no final

    // Valida o ISBN
    if (strlen(novo_livro.isbn) == 0 || !validarIsbn(novo_livro.isbn)) {
        printf("Erro: ISBN invalido! O ISBN deve conter 13 digitos numericos.\n");
        return;
    }

    // Atribui um ID único ao livro e define que ele está disponível
    novo_livro.id = totalLivros + 1;
    novo_livro.disponivel = 1;  // O livro está disponível inicialmente

    // Adiciona o livro no array de livros e incrementa o contador
    livros[totalLivros] = novo_livro;
    totalLivros++;

    // Confirma que o livro foi cadastrado com sucesso
    printf("Livro '%s' cadastrado com sucesso!\n", novo_livro.titulo);
}


/**
 * @brief Função para excluir um livro do sistema.
 *
 * Solicita o ID de um livro, verifica se ele existe no sistema e se está disponível para ser excluído.
 */
void excluirLivro() {
    int idLivro;

    // Solicita o ID do livro a ser excluído
    printf("Digite o ID do livro a ser excluido: ");
    scanf("%d", &idLivro);

    // Verifica se o livro existe
    if (idLivro < 1 || idLivro > totalLivros) {
        printf("Livro nao encontrado.\n");
        return;
    }

    // Verifica se o livro está emprestado
    for (int i = 0; i < totalClientes; i++) {
        if (clientes[i].livroEmprestadoId == idLivro) {
            printf("Este livro esta emprestado e nao pode ser excluido.\n");
            return;
        }
    }

    // Remove o livro do array, deslocando os livros seguintes para trás
    for (int i = idLivro - 1; i < totalLivros - 1; i++) {
        livros[i] = livros[i + 1];
    }

    totalLivros--;  // Decrementa o número de livros cadastrados
    printf("Livro excluido com sucesso.\n");
}


/**
 * @brief Função para listar todos os livros cadastrados no sistema.
 *
 * Exibe informações sobre todos os livros, incluindo título, autor, ISBN, e seu status (disponível ou emprestado).
 */
void listarLivros() {
    // Verifica se não há livros cadastrados
    if (totalLivros == 0) {
        printf("Nenhum livro cadastrado.\n");
        return;
    }

    // Exibe a lista de livros
    printf("Lista de livros:\n");
    for (int i = 0; i < totalLivros; i++) {
        Livro *livro = &livros[i];
        printf("ID: %d\n Titulo: %s\n Autor: %s\n ISBN: %s\n Status: %s\n",
               livro->id, livro->titulo, livro->autor, livro->isbn,
               livro->disponivel ? "Disponivel" : "Emprestado");

        // Se o livro está emprestado, exibe o ID do cliente que pegou
        if (!livro->disponivel) {
            for (int j = 0; j < totalClientes; j++) {
                if (clientes[j].livroEmprestadoId == livro->id) {
                    printf(" Emprestado para o cliente ID: %d\n", clientes[j].id);
                    break;
                }
            }
        }
        printf("\n");
    }
}

livro.h

#ifndef PROJETO04_LIVRO_H
#define PROJETO04_LIVRO_H

// Define o número máximo de livros permitidos no sistema
#define MAX_LIVROS 100

/**
 * @brief Estrutura que define as informações de um livro.
 *
 * A estrutura Livro armazena dados como o ID do livro, título, autor, ISBN e se o livro está disponível para empréstimo.
 */
typedef struct {
    int id;             /< Identificador único do livro */
    char titulo[100];   /< Título do livro */
    char autor[100];    /< Autor do livro */
    char isbn[14];      /< ISBN do livro (somente números) */
    int disponivel;     /< Indica se o livro está disponível para empréstimo (1 - disponível, 0 - emprestado) */
} Livro;

// Declarações de variáveis externas
extern Livro livros[MAX_LIVROS];     /< Array de livros cadastrados */
extern int totalLivros;              /< Total de livros cadastrados no sistema */

// Declarações das funções relacionadas ao livro
void cadastrarLivro();  /< Função para cadastrar um novo livro */
void excluirLivro();    /< Função para excluir um livro existente */
void listarLivros();    /< Função para listar todos os livros cadastrados */


#endif //PROJETO04_LIVRO_H

validacao.c

#include <stdio.h>
#include <string.h>
#include <ctype.h>
#include "validacao.h"

/**
 * @brief Valida um CPF, verificando se ele possui 11 dígitos numéricos.
 *
 * A função verifica se o CPF tem exatamente 11 caracteres e se todos são números.
 *
 * @param cpf O CPF a ser validado.
 * @return Retorna 1 se o CPF for válido, 0 caso contrário.
 */
int validarCpf(char cpf[]) {
    // Verifica se o CPF tem exatamente 11 caracteres
    if (strlen(cpf) != 11) {
        return 0;
    }

    // Verifica se todos os caracteres do CPF são números
    for (int i = 0; i < 11; i++) {
        if (!isdigit(cpf[i])) {
            return 0;
        }
    }
    return 1; // CPF válido
}


/**
 * @brief Valida um ISBN, verificando se ele possui 13 dígitos numéricos.
 *
 * A função verifica se o ISBN tem exatamente 13 caracteres e se todos são números.
 *
 * @param isbn O ISBN a ser validado.
 * @return Retorna 1 se o ISBN for válido, 0 caso contrário.
 */
int validarIsbn(char isbn[]) {
    // Verifica se o ISBN tem exatamente 13 caracteres
    if (strlen(isbn) != 13) {
        return 0;
    }

    // Verifica se todos os caracteres do ISBN são números
    for (int i = 0; i < 13; i++) {
        if (!isdigit(isbn[i])) {
            return 0;
        }
    }
    return 1; // ISBN válido
}


/**
 * @brief Valida um número de telefone, verificando se ele possui 11 dígitos numéricos.
 *
 * A função verifica se o número de telefone tem exatamente 11 caracteres e se todos são números.
 *
 * @param telefone O telefone a ser validado.
 * @return Retorna 1 se o telefone for válido, 0 caso contrário.
 */
int validarTelefone(char telefone[]) {
    // Verifica se o telefone tem exatamente 11 caracteres
    if (strlen(telefone) != 11) {
        return 0;
    }

    // Verifica se todos os caracteres do telefone são números
    for (int i = 0; i < 11; i++) {
        if (!isdigit(telefone[i])) {
            return 0;
        }
    }
    return 1; // Telefone válido
}

validacao.h

#ifndef PROJETO04_VALIDACAO_H
#define PROJETO04_VALIDACAO_H

// Função para validar o CPF
// Retorna 1 se o CPF for válido (contiver 11 dígitos numéricos), e 0 caso contrário.
int validarCpf(char cpf[]);

// Função para validar o ISBN
// Retorna 1 se o ISBN for válido (contiver 13 dígitos numéricos), e 0 caso contrário.
int validarIsbn(char isbn[]);

// Função para validar o telefone
// Retorna 1 se o telefone for válido (contiver 11 dígitos numéricos), e 0 caso contrário.
int validarTelefone(char telefone[]);


#endif //PROJETO04_VALIDACAO_H
