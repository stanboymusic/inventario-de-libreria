# inventario-de-libreria
Angel David  Rondon Contreras
c.i 31671702
seccion 9

adrcproducciones@gmail.com
angel.rondon@unet.edu.ve

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h> // Para toupper y isdigit

// Función para convertir una cadena a mayúsculas
void toUpperCase(char *str) {
    int i; // Mueve la declaración de 'i' aquí
    for (i = 0; str[i] != '\0'; i++) {
        str[i] = (char)toupper((unsigned char)str[i]);
    }
}

// Función para imprimir texto en color 
void printColor(const char *text, const char *color) {
    if (strcmp(color, "red") == 0) {
        printf("\033[1;31m%s\033[0m", text); // Rojo brillante
    } else if (strcmp(color, "green") == 0) {
        printf("\033[1;32m%s\033[0m", text); // Verde brillante
    } else {
        printf("%s", text); // Sin color
    }
}

int main() {
    int n;
    char **titulo;
    char **autor;
    char **genero;
    char **ISBN_str; 
    float *precio;
    int *stock;
    float *ganancia;
    int i; // Mueve la declaración de 'i' aquí para el primer for loop

    printf("INGRESE LA CANTIDAD DE LIBROS A REGISTRAR: ");
    // Validar que la entrada sea un número positivo para n
    while (scanf("%d", &n) != 1 || n <= 0) {
        printf("CANTIDAD INVALIDA. POR FAVOR, INGRESE UN NUMERO POSITIVO: ");
        while (getchar() != '\n'); // Limpiar el búfer de entrada
    }
    while (getchar() != '\n'); // Limpiar el búfer después de scanf

    // Asignación dinámica de memoria para los vectores paralelos
    titulo = (char **)malloc(n * sizeof(char *));
    autor = (char **)malloc(n * sizeof(char *));
    genero = (char **)malloc(n * sizeof(char *));
    ISBN_str = (char **)malloc(n * sizeof(char *));
    precio = (float *)malloc(n * sizeof(float));
    stock = (int *)malloc(n * sizeof(int));
    ganancia = (float *)malloc(n * sizeof(float));

    if (titulo == NULL || autor == NULL || genero == NULL || ISBN_str == NULL ||
        precio == NULL || stock == NULL || ganancia == NULL) {
        printf("ERROR: NO SE PUDO ASIGNAR MEMORIA.\n");
        return 1;
    }

    for (i = 0; i < n; i++) {
        int j; // Declaración para el loop interno de liberación de memoria
        titulo[i] = (char *)malloc(50 * sizeof(char)); // Tamaño adecuado para cadenas
        autor[i] = (char *)malloc(50 * sizeof(char));
        genero[i] = (char *)malloc(50 * sizeof(char));
        ISBN_str[i] = (char *)malloc(11 * sizeof(char)); // 10 dígitos + '\0'

        if (titulo[i] == NULL || autor[i] == NULL || genero[i] == NULL || ISBN_str[i] == NULL) {
            printf("ERROR: NO SE PUDO ASIGNAR MEMORIA PARA EL LIBRO %d.\n", i + 1);
            // Liberar la memoria ya asignada antes de salir
            for (j = 0; j <= i; j++) { // Usa 'j' para este loop
                free(titulo[j]);
                free(autor[j]);
                free(genero[j]);
                free(ISBN_str[j]);
            }
            free(titulo); free(autor); free(genero); free(ISBN_str);
            free(precio); free(stock); free(ganancia);
            return 1;
        }

        printf("\n--- INGRESE DATOS PARA EL LIBRO %d ---\n", i + 1);

        printf("INGRESE EL TITULO: ");
        fgets(titulo[i], 50, stdin);
        titulo[i][strcspn(titulo[i], "\n")] = 0; // Eliminar el salto de línea
        toUpperCase(titulo[i]);

        printf("INGRESE EL AUTOR: ");
        fgets(autor[i], 50, stdin);
        autor[i][strcspn(autor[i], "\n")] = 0;
        toUpperCase(autor[i]);

        printf("INGRESE EL GENERO: ");
        fgets(genero[i], 50, stdin);
        genero[i][strcspn(genero[i], "\n")] = 0;
        toUpperCase(genero[i]);

        // Validar ISBN
        int isbn_valido = 0;
        while (!isbn_valido) {
            printf("INGRESE EL CODIGO ISBN (10 DIGITOS NUMERICOS): ");
            fgets(ISBN_str[i], 11, stdin); // Lee hasta 10 caracteres + '\0'
            ISBN_str[i][strcspn(ISBN_str[i], "\n")] = 0; // Eliminar salto de línea si existe

            // Asegurarse de limpiar el buffer si se ingresan más de 10 caracteres
            if (strlen(ISBN_str[i]) == 10 && (ISBN_str[i][10] != '\0' && ISBN_str[i][10] != '\n')) {
                 int c;
                 while ((c = getchar()) != '\n' && c != EOF);
            }

            if (strlen(ISBN_str[i]) == 10) {
                int es_numerico = 1;
                int k; // Mueve la declaración de 'k' aquí
                for (k = 0; k < 10; k++) {
                    if (!isdigit((unsigned char)ISBN_str[i][k])) {
                        es_numerico = 0;
                        break;
                    }
                }
                if (es_numerico) {
                    isbn_valido = 1;
                } else {
                    printf("ISBN INVALIDO. DEBE CONTENER SOLO DIGITOS NUMERICOS.\n");
                }
            } else {
                printf("ISBN INVALIDO. DEBE TENER EXACTAMENTE 10 DIGITOS.\n");
            }
        }
        toUpperCase(ISBN_str[i]); // Convertir a mayúsculas (aunque serán números, por consistencia)

        // Validar Precio unitario
        int precio_valido = 0;
        while (!precio_valido) {
            printf("INGRESE EL PRECIO UNITARIO: ");
            if (scanf("%f", &precio[i]) == 1) {
                if (precio[i] >= 0) {
                    precio_valido = 1;
                } else {
                    printf("PRECIO INVALIDO. NO PUEDE SER NEGATIVO.\n");
                }
            } else {
                printf("ENTRADA INVALIDA. POR FAVOR, INGRESE UN NUMERO VALIDO PARA EL PRECIO.\n");
                while (getchar() != '\n'); // Limpiar el búfer de entrada
            }
            while (getchar() != '\n'); // Limpiar el búfer después de scanf
        }

        printf("INGRESE LA CANTIDAD DISPONIBLE EN INVENTARIO (STOCK): ");
        // Validar que la entrada sea un número
        while (scanf("%d", &stock[i]) != 1) {
            printf("ENTRADA INVALIDA. POR FAVOR, INGRESE UN NUMERO VALIDO PARA EL STOCK: ");
            while (getchar() != '\n'); // Limpiar el búfer de entrada
        }
        while (getchar() != '\n'); // Limpiar el búfer después de scanf

        ganancia[i] = stock[i] * precio[i];
    }

    // --- MOSTRAR TODOS LOS DATOS EN PANTALLA ---
    printf("\n--- INVENTARIO DE LIBROS ---\n");
    printf("%-20s %-20s %-15s %-15s %-10s %-10s %-10s\n",
           "TITULO", "AUTOR", "GENERO", "ISBN", "PRECIO", "STOCK", "GANANCIA");
    printf("--------------------------------------------------------------------------------------------------------\n");

    for (i = 0; i < n; i++) {
        printf("%-20s %-20s %-15s %-15s %-10.2f %-10d ",
               titulo[i], autor[i], genero[i], ISBN_str[i], precio[i], stock[i]);
        if (ganancia[i] < 0) {
            printColor("", "red"); // Iniciar color rojo
            printf("%-10.2f\n", ganancia[i]);
            printf("\033[0m"); // Resetear color
        } else {
            printColor("", "green"); // Iniciar color verde
            printf("%-10.2f\n", ganancia[i]);
            printf("\033[0m"); // Resetear color
        }
    }
    printf("\n");

    // --- FILTRAR INFORMACIÓN ---
    char opcion_filtro_str[2];
    int opcion_filtro;
    char valor_filtro[50];
    int libros_encontrados = 0;
    int j; // Declaración para el loop de liberación de memoria

    printf("¿DESEA FILTRAR LA INFORMACION? (1 = SI, 0 = NO): ");
    while (scanf("%1s", opcion_filtro_str) != 1 || (opcion_filtro_str[0] != '0' && opcion_filtro_str[0] != '1')) {
        printf("OPCION INVALIDA. INGRESE 1 PARA SI O 0 PARA NO: ");
        while (getchar() != '\n');
    }
    opcion_filtro = atoi(opcion_filtro_str);
    while (getchar() != '\n'); // Limpiar el buffer

    if (opcion_filtro == 1) {
        printf("\n--- FILTRAR POR ---\n");
        printf("1. TITULO\n");
        printf("2. AUTOR\n");
        printf("3. GENERO\n");
        printf("4. ISBN\n");
        printf("SELECCIONE UNA OPCION DE FILTRADO: ");

        char filtro_valido_str[2];
        int filtro_valido;
        while (scanf("%1s", filtro_valido_str) != 1 || (filtro_valido_str[0] < '1' || filtro_valido_str[0] > '4')) {
            printf("OPCION INVALIDA. INGRESE UN NUMERO ENTRE 1 Y 4: ");
            while (getchar() != '\n');
        }
        filtro_valido = atoi(filtro_valido_str);
        while (getchar() != '\n'); // Limpiar el buffer

        printf("INGRESE EL VALOR A BUSCAR: ");
        fgets(valor_filtro, 50, stdin);
        valor_filtro[strcspn(valor_filtro, "\n")] = 0;
        toUpperCase(valor_filtro);

        printf("\n--- RESULTADOS DE FILTRADO ---\n");
        for (i = 0; i < n; i++) { // 'i' ya está declarado
            int coincide = 0;
            switch (filtro_valido) {
                case 1: // Titulo
                    if (strstr(titulo[i], valor_filtro) != NULL) coincide = 1;
                    break;
                case 2: // Autor
                    if (strstr(autor[i], valor_filtro) != NULL) coincide = 1;
                    break;
                case 3: // Genero
                    if (strstr(genero[i], valor_filtro) != NULL) coincide = 1;
                    break;
                case 4: // ISBN
                    if (strstr(ISBN_str[i], valor_filtro) != NULL) coincide = 1;
                    break;
            }

            if (coincide) {
                libros_encontrados++;
                printf("LIBRO ENCONTRADO: TITULO: %s | AUTOR: %s | GENERO: %s | ISBN: %s | PRECIO: %.2f | STOCK: %d | GANANCIA: ",
                       titulo[i], autor[i], genero[i], ISBN_str[i], precio[i], stock[i]);
                if (ganancia[i] < 0) {
                    printColor("", "red");
                    printf("%.2f\n", ganancia[i]);
                    printf("\033[0m");
                } else {
                    printColor("", "green");
                    printf("%.2f\n", ganancia[i]);
                    printf("\033[0m");
                }
            }
        }
        if (libros_encontrados == 0) {
            printf("NO SE ENCONTRARON LIBROS CON EL VALOR ESPECIFICADO.\n");
        }
    }

    // Liberar la memoria asignada
    for (j = 0; j < n; j++) { // Usa 'j' para este loop
        free(titulo[j]);
        free(autor[j]);
        free(genero[j]);
        free(ISBN_str[j]);
    }
    free(titulo);
    free(autor);
    free(genero);
    free(ISBN_str);
    free(precio);
    free(stock);
    free(ganancia);

    return 0;
}
