Programação Paralela

Henrique Becker Brum


Pthreads:

1.
    a)Particionamento: Acontece quando cada thread recebe um offest para tratar de uma parte individual do vetor.

      Em void dotprod_threads(int nthreads):

      ```
      for (i = 0; i < nthreads; i++) {
         pthread_create(&threads[i], &attr, dotprod_worker, (void *) i);
      }

      ```
      Em void* dotprod_worker(void* arg):

      ```
      int start = offset*wsize;
      int end = start + wsize;

      for (k = 0; k < dotdata.repeat; k++) {
        mysum = 0.0;
        for (i = start; i < end ; i++)  {
           mysum += (a[i] * b[i]);
        }
     }
      ```

    b)Comunicação: Ocorre quando todos as threads precisam fazer o calculo da multiplicação entre
    cada posição e a soma dessas multiplicações.

      Em void* dotprod_worker(void* arg):

      ```

      for (k = 0; k < dotdata.repeat; k++) {
         mysum = 0.0;
         for (i = start; i < end ; i++)  {
           //A comunicação é o '(a[i] * b[i])'
            mysum += (a[i] * b[i]);
         }
      }


      pthread_mutex_lock (&mutexsum);
      //A comunicação ocorre quando dotdata.c recebe dados de todas as threads
      dotdata.c += mysum;
      pthread_mutex_unlock (&mutexsum);

      ```
    c)Aglomeração: Ocorre quando é feito multiplas subsomas tanto no processo de multiplicação quanto
      no processo do somatório final.

      Em void* dotprod_worker(void* arg):

      ```

      for (k = 0; k < dotdata.repeat; k++) {
         mysum = 0.0;
         for (i = start; i < end ; i++)  {
           // A aglomeração é o 'mysum +='
            mysum += (a[i] * b[i]);
         }
      }

      pthread_mutex_lock (&mutexsum);
      //A comunicação é o 'dotdata.c +='
      dotdata.c += mysum;
      pthread_mutex_unlock (&mutexsum);

      ```

      d)Mapeamento: Acontece quando há divisão ou controle das threads.

        Em void dotprod_threads(int nthreads):

        ```

        for (i = 0; i < nthreads; i++) {
          pthread_join(threads[i], NULL);
        }

        ```


2.
    Speedup = 5227699/3832217 = 1,364144828

3.
  Sim. Na maioria dos casos quando se há um incremento no npumero de threads com a mesma
  quantidade de iterações o tempo diminui. Com números maiores de thrad o aumento como a ser
  menor ou as vezes não há Speedup.
4.

| Tamanho Vetor    | Número de Repetições | Quantidade de Threads | Tempo (usec)(x-barra (5)) |
|------------------|:--------------------:|-----------------------|---------------------------|
| 10^6             | 2000                 | 1                     | 5227699                   |
| 10^6 / 2         | 2000                 | 2                     | 3832217                   |
| 10^6 / 4         | 2000                 | 4                     | 2349076                   |
| 10^6 / 8         | 2000                 | 8                     | 2409288                   |
| 10^6 / 16        | 2000                 | 16                    | 2069955                   |


Speedup(1 -> 2) = 1,364144828
Speedup(1 -> 8) = 2,169810749
Speedup(2 -> 4) = 1,63137208
Speedup(4 -> 8) = 0,975008384
Speedup(4 -> 16) = 1,134843994

+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

| Tamanho Vetor    | Número de Repetições | Quantidade de Threads | Tempo (usec)(x-barra (5))  |
|------------------|:--------------------:|-----------------------|----------------------------|
| 10^7             | 2000                 | 1                     | 56739979                   |
| 10^7 / 2         | 2000                 | 2                     | 34666435                   |
| 10^7 / 4         | 2000                 | 4                     | 27571100                   |
| 10^7 / 8         | 2000                 | 8                     | 26091783                   |
| 10^7 / 16        | 2000                 | 16                    | 25974455                   |


Speedup(1 -> 2) = 1,636741101
Speedup(1 -> 8) = 2,174630189
Speedup(2 -> 4) = 1,257346823
Speedup(4 -> 8) = 1,056696662
Speedup(4 -> 16) = 1,061469817

+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

| Tamanho Vetor    | Número de Repetições | Quantidade de Threads | Tempo (usec)(x-barra (5))  |
|------------------|:--------------------:|-----------------------|----------------------------|
| 10^7             | 1000                 | 1                     | 29538580                   |
| 10^7 / 2         | 1000                 | 2                     | 17878511                   |
| 10^7 / 4         | 1000                 | 4                     | 12010590                   |
| 10^7 / 8         | 1000                 | 8                     | 11729442                   |
| 10^7 / 16        | 1000                 | 16                    | 12091746                   |


Speedup(1 -> 2) = 1,652183451
Speedup(1 -> 8) = 2,518327811
Speedup(2 -> 4) = 1,48856226
Speedup(4 -> 8) = 1,023969427
Speedup(4 -> 16) = 0,993288314

+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

| Tamanho Vetor    | Número de Repetições | Quantidade de Threads | Tempo (usec)(x-barra (5))  |
|------------------|:--------------------:|-----------------------|----------------------------|
| 10^8             | 100                  | 1                     | 29780235                   |
| 10^8 / 2         | 100                  | 2                     | 17366558                   |
| 10^8 / 4         | 100                  | 4                     | 12930538                   |
| 10^8 / 8         | 100                  | 8                     | 12129372                   |
| 10^8 / 16        | 100                  | 16                    | 11955772                   |

Speedup(1 -> 2) = 1,714803532
Speedup(1 -> 8) = 2,45521656
Speedup(2 -> 4) = 1,343065385
Speedup(4 -> 8) = 1,06605173
Speedup(4 -> 16) = 1,081530996


5.
      O código pthreads_dotprod.c possui exclusão mutua na hora de acessar o dotdata.c, já o pthreads_dotprod2.code
    não.

      Nesse caso o resultado fica igual pois não importa a ordem de acesso à esse dado e nem o valor do mesmo.
      Pois se uma thread fossse incrementar dotdata.c e logo antes de fazer ADD X, Y, Z outra thread interrompesse
      e se somasse à dotdata.c o valor final de dotdata.c ficaria igual.




OpenMP:






Referências:
