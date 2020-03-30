#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <pthread.h>
#include <stdbool.h>
#include <time.h>

int p;
int r;
int availR[10];
int Allocated[10][10];
int MaxRequired[10][10];
int RemNeed[10][10];
int SafeSeq[10];
int nProcessRan = 0;

pthread_mutex_t lockResources;
pthread_cond_t condition;
int getSafeSeq() {
    // get safe sequence
        int tempRes[r];
        for(int i=0; i<r; i++) 
            tempRes[i] =availR[i];

        bool finished[p];
        for(int i=0; i<p; i++) finished[i] = false;
        int nfinished=0;
        while(nfinished < p) {
                bool safe = false;
          for(int i=0; i<p; i++) {
                        if(!finished[i]) {
                                bool possible = true;

                                for(int j=0; j<r; j++)
                                        if(RemNeed[i][j] > tempRes[j]) {
                                                possible = false;
                                                break;
                                        }

                                if(possible) {
                                        for(int j=0; j<r; j++)
                                                tempRes[j] += Allocated[i][j];
                                        SafeSeq[nfinished] = i;
                                        finished[i] = true;
                                        ++nfinished;
                                        safe = true;
                                }
                        }
                }

                if(!safe) {
                        for(int k=0; k<p; k++) SafeSeq[k] = -1;
                        return false; // no safe sequence found
                }
        }
        return 1; // safe sequence found
}
// main process code
void* processCode(void *arg) {
        int p = *((int *) arg);

    // lockResources using mutex lock
        pthread_mutex_lock(&lockResources);

        // condition check
        while(p != SafeSeq[nProcessRan])
                pthread_cond_wait(&condition, &lockResources);

    // process
        printf("\n--> Process %d", p+1);
        printf("\n\tAllocated : ");
        for(int i=0; i<r; i++)
                printf("%3d", Allocated[p][i]);

        printf("\n\tNeeded    : ");
        for(int i=0; i<r; i++)
                printf("%3d", RemNeed[p][i]);

        printf("\n\tAvailable : ");
        for(int i=0; i<r; i++)
                printf("%3d",availR[i]);

        printf("\n"); sleep(1);

        printf("\tResource Allocated!");
        printf("\n"); sleep(1);
        printf("\tProcess Code Running...");
        printf("\n"); sleep(rand()%3 + 2); // process code
        printf("\tProcess Code Completed...");
        printf("\n"); sleep(1);
        printf("\tProcess Releasing Resource...");
        printf("\n"); sleep(1);
        printf("\tResource Released!");

    for(int i=0; i<r; i++)
               availR[i] += Allocated[p][i];


        printf("\n\tNow Available : ");
        for(int i=0; i<r; i++)
                printf("%3d",availR[i]);
        printf("\n\n");

        sleep(1);

    // condition broadcast
        nProcessRan++;
        pthread_cond_broadcast(&condition);
        pthread_mutex_unlock(&lockResources);
    pthread_exit(NULL);
}


 int main() {
	srand(time(0));

        printf("\nEnter the number of processes? ");
        scanf("%d", &p);

        printf("\nEnter the number of resources? ");
        scanf("%d", &r);

       //Cuurently available resources=
        printf("\nCurrently Available Resources (R1 R2 ...)? (considered after allocation)");
        for(int i=0; i<r; i++)
                scanf("%d", &availR[i]);

        
        

        // Allocated resources to each specific process
        printf("\n");
        for(int i=0; i<p; i++) {
                printf("\nResource allocated to process %d (R1 R2 ...)? ", i+1);
                for(int j=0; j<r; j++)
                        scanf("%d", &Allocated[i][j]);
        }
        printf("\n");

	// maximum required Resources
        for(int i=0; i<p; i++) {
                printf("\nMaximum resource required by process %d (R1 R2 ...)? ", i+1);
                for(int j=0; j<r; j++)
                        scanf("%d", &MaxRequired[i][j]);
        }
        printf("\n");
	// calculate  matrix for need[i]
       
        for(int i=0; i<p; i++)
                for(int j=0; j<r; j++)
                       RemNeed[i][j] = MaxRequired[i][j] - Allocated[i][j];

	// to obtain safe sequence
        for(int i=0; i<p; i++) SafeSeq[i] = -1;

        if(!getSafeSeq()) {
                printf("\nUnsafe State! The processes leads the system to a unsafe state.\n\n");
                exit(-1);
        }
        printf("\n\nSafe Sequence Found : ");
        for(int i=0; i<p; i++) {
                printf("%-3d", SafeSeq[i]+1);
        }

        printf("\nExecuting Processes...\n\n");
        sleep(1);
	// run threads
	pthread_t processes[p];
        pthread_attr_t attr;
        pthread_attr_init(&attr);

	int ProcessNum[p];
	for(int i=0; i<p; i++) ProcessNum[i] = i;
        for(int i=0; i<p; i++)
                pthread_create(&processes[i], &attr, processCode, (void *)(&ProcessNum[i]));
        for(int i=0; i<p; i++)
                pthread_join(processes[i], NULL);

        printf("\nAll Processes Finished\n");	
	
}








                
