# TRABALHO-ESTACIO

#include <stdio.h>
#include <string.h>

char *diasSemana[] = {
    "Segunda",
    "Terca",
    "Quarta",
    "Quinta",
    "Sexta",
    "Sabado",
    "Domingo"
};

void listarRemedios() {

    FILE *lista;
    char texto[100];

    lista = fopen("remedios.txt", "r");

    if(lista == NULL) {
        printf("Nenhum remedio cadastrado.\n");
        return;
    }

    printf("\n=== REMEDIOS CADASTRADOS ===\n\n");

    while(fgets(texto, sizeof(texto), lista) != NULL) {
        printf("%s", texto);
    }

    fclose(lista);
}

int remedioExiste(char nome[]) {

    FILE *lista;
    char remedio[50];

    lista = fopen("remedios.txt", "r");

    if(lista == NULL)
        return 0;

    while(fgets(remedio, sizeof(remedio), lista) != NULL) {

        remedio[strcspn(remedio, "\n")] = '\0';

        if(strcmp(remedio, nome) == 0) {
            fclose(lista);
            return 1;
        }
    }

    fclose(lista);
    return 0;
}

void cadastrarRemedio() {

    char nomeRemedio[50];
    char especificacao[100];
    char horario[50];
    char arquivoNome[60];

    FILE *arquivo;
    FILE *lista;

    printf("\nNome do remedio: ");
    scanf("%49s", nomeRemedio);

    strcpy(arquivoNome, nomeRemedio);
    strcat(arquivoNome, ".txt");

    arquivo = fopen(arquivoNome, "w");

    if(arquivo == NULL) {
        printf("Erro ao cadastrar remedio!\n");
        return;
    }

    printf("Especificacoes: ");
    scanf(" %[^\n]", especificacao);

    printf("Horario para tomar o remedio: ");
    scanf(" %[^\n]", horario);

    fprintf(arquivo, "Especificacoes: %s\n", especificacao);
    fprintf(arquivo, "Horario: %s\n", horario);

    fclose(arquivo);

    if(!remedioExiste(nomeRemedio)) {

        lista = fopen("remedios.txt", "a");

        if(lista != NULL) {
            fprintf(lista, "%s\n", nomeRemedio);
            fclose(lista);
        }
    }

    printf("Remedio salvo com sucesso!\n");
}

void cadastrarDias() {

    char nomeRemedio[50];
    char arquivoDias[60];
    int dia;
    int resultado;

    FILE *arquivo;
    FILE *lista;

    lista = fopen("remedios.txt", "r");

    if(lista == NULL) {
        printf("Nenhum remedio cadastrado.\n");
        return;
    }

    fclose(lista);

    listarRemedios();

    printf("\nDigite o nome do remedio: ");
    scanf("%49s", nomeRemedio);

    strcpy(arquivoDias, nomeRemedio);
    strcat(arquivoDias, "_dias.txt");

    arquivo = fopen(arquivoDias, "w");

    if(arquivo == NULL) {
        printf("Erro ao salvar os dias.\n");
        return;
    }

    printf("\n1 - Segunda\n");
    printf("2 - Terca\n");
    printf("3 - Quarta\n");
    printf("4 - Quinta\n");
    printf("5 - Sexta\n");
    printf("6 - Sabado\n");
    printf("7 - Domingo\n");
    printf("0 - Finalizar\n\n");

    do {

        printf("Dia: ");

        resultado = scanf("%d", &dia);

        if(resultado != 1) {

            printf("Erro! Digite apenas numeros entre 0 e 7.\n");

            while(getchar() != '\n');

            continue;
        }

        if(dia < 0 || dia > 7) {

            printf("Opcao invalida! Escolha um numero entre 0 e 7.\n");

            continue;
        }

        if(dia >= 1 && dia <= 7) {
            fprintf(arquivo, "%s\n", diasSemana[dia - 1]);
        }

    } while(dia != 0);

    fclose(arquivo);

    printf("Dias cadastrados com sucesso!\n");
}

void mostrarCronograma() {

    FILE *lista;
    FILE *arquivo;

    char remedio[50];
    char arquivoDias[60];
    char diaSalvo[30];

    int i;

    lista = fopen("remedios.txt", "r");

    if(lista == NULL) {
        printf("Nenhum remedio cadastrado.\n");
        return;
    }

    printf("\n===== CRONOGRAMA SEMANAL =====\n");

    for(i = 0; i < 7; i++) {

        printf("\n%s:\n", diasSemana[i]);

        rewind(lista);

        while(fgets(remedio, sizeof(remedio), lista) != NULL) {

            char arquivoInfo[60];
            char linha[100];
            char horario[100] = "";

            remedio[strcspn(remedio, "\n")] = '\0';

            strcpy(arquivoDias, remedio);
            strcat(arquivoDias, "_dias.txt");

            arquivo = fopen(arquivoDias, "r");

            if(arquivo != NULL) {

                while(fgets(diaSalvo, sizeof(diaSalvo), arquivo) != NULL) {

                    diaSalvo[strcspn(diaSalvo, "\n")] = '\0';

                    if(strcmp(diaSalvo, diasSemana[i]) == 0) {

                        strcpy(arquivoInfo, remedio);
                        strcat(arquivoInfo, ".txt");

                        FILE *info = fopen(arquivoInfo, "r");

                        if(info != NULL) {

                            while(fgets(linha, sizeof(linha), info) != NULL) {

                                if(strncmp(linha, "Horario:", 8) == 0) {

                                    strcpy(horario, linha + 9);
                                    horario[strcspn(horario, "\n")] = '\0';
                                    break;
                                }
                            }

                            fclose(info);
                        }

                        printf("- %s (%s)\n", remedio, horario);
                        break;
                    }
                }

                fclose(arquivo);
            }
        }
    }

    fclose(lista);
}

int main() {

    int opcao = 0;

    while(opcao != 5) {

        printf("\n========== MENU ==========\n");
        printf("1 - CADASTRAR REMEDIO\n");
        printf("2 - REMEDIOS CADASTRADOS\n");
        printf("3 - CADASTRAR DIAS DO REMEDIO\n");
        printf("4 - CRONOGRAMA SEMANAL\n");
        printf("5 - SAIR\n");
        printf("==========================\n");
        printf("Escolha uma opcao: ");

        if(scanf("%d", &opcao) != 1) {

            printf("Erro! Digite apenas numeros.\n");

            while(getchar() != '\n');

            continue;
        }

        switch(opcao) {

            case 1:
                cadastrarRemedio();
                break;

            case 2:
                listarRemedios();
                break;

            case 3:
                cadastrarDias();
                break;

            case 4:
                mostrarCronograma();
                break;

            case 5:
                printf("Encerrando programa...\n");
                break;

            default:
                printf("Opcao invalida!\n");
        }
    }

    return 0;
}
