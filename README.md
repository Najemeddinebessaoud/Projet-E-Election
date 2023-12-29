# Projet-E-Election
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef struct {
    char* nom;
    char* vote;
} candidat;

struct election {
    int annee;
    candidat val;
    char resultat[50];
    int nbV;
    struct election* suiv;
};

struct cellule {
    struct election* val;
    int annee;
    struct cellule* suiv;
};

struct election* creer_elec(int anne) {
    struct election* la = (struct election*)malloc(sizeof(struct election));
    la->annee = anne;
    la->suiv = NULL;
    la->nbV = 0;
    return la;
}

void ajout_candidat(char* nom, char* vote, int anne, struct cellule* ka) {
    struct election* aux, * p, * l;
    struct cellule* kak;
    kak = ka;
    int t = 0;
    while ((*kak).suiv != NULL) {
        if (kak->annee == anne) {
            l = kak->val;
            candidat c;
            c.nom = strdup(nom);
            c.vote = strdup(vote);
            aux = l;
            while ((*aux).suiv != NULL) {
                aux = (*aux).suiv;
            }
            p = (struct election*)malloc(sizeof(struct election));
            p->val = c;
            p->suiv = NULL;
            (*aux).suiv = p;
        }
        kak = kak->suiv;
    }
    if (t == 0) {
        printf("Election introuvable\n");
    }
}

void ajout_election(struct election* a, struct cellule* l) {
    struct cellule* aux, * p;
    aux = l;
    while ((*aux).suiv != NULL) {
        aux = (*aux).suiv;
    }
    p = (struct cellule*)malloc(sizeof(struct cellule));
    p->val = a;
    p->suiv = NULL;
    (*aux).suiv = p;
}

void voteee(struct election* l) {
    struct election* aux, * p;
    aux = l;
    p = l;
    while ((*aux).suiv != NULL) {
        while ((*p).suiv != NULL) {
            if (strcmp(aux->val.nom, p->val.vote) == 0) {
                aux->nbV++;
            }
            p = p->suiv;
        }
        aux = aux->suiv;
    }
}

void change_vote(struct cellule* m, int anne, char nom[50], char V[50]) {
    struct cellule* pa;
    struct election* l;
    int t = 0;
    pa = m;
    while ((*pa).suiv != NULL) {
        if (pa->val->annee == anne) {
            l = pa->val;
            struct election* aux, * p;
            aux = l;
            while ((*aux).suiv != NULL) {
                if (strcmp(aux->val.nom, nom) == 0) {
                    t++;
                    strcpy(aux->val.vote, V);
                }
                aux = aux->suiv;
            }
        }
        pa = pa->suiv;
    }
    if (t == 0) {
        printf("Candidat introuvable\n");
    }
}

void affiche_election(struct cellule* l, int anne) {
    struct election* p;
    struct cellule* aux;
    aux = l;
    while ((*aux).suiv != NULL) {
        if (aux->val->annee == anne) {
            p = aux->val;
            while ((p)->suiv != NULL) {
                printf("%s %d %s\n", p->val.nom, p->nbV, p->resultat);
                p = p->suiv;
            }
        }
        aux = aux->suiv;
    }
}

void affiche_liste_vote(struct cellule* c, int anne, char nom[50]) {
    struct cellule* aux;
    struct election* p;
    aux = c;
    while ((*aux).suiv != NULL) {
        if (aux->annee == anne) {
            p = aux->val;
            while ((*p).suiv != NULL) {
                if (strcmp(p->val.vote, nom) == 0) {
                    printf("%s\n", p->val.nom);
                }
                p = p->suiv;
            }
        }
        aux = aux->suiv;
    }
}

int main() {
    struct cellule* m = NULL;
    int r = 1;

    while (r != 6) {
        printf("1. Ajout candidat\n");
        printf("2. Creer election\n");
        printf("3. Change Vote\n");
        printf("4. Affiche election\n");
        printf("5. Affiche liste de vote\n");
        printf("6. Quitter\n");
        printf("Choix: ");
        scanf("%d", &r);

        if (r == 6) {
            printf("Au revoir!\n");
            break;
        }
        else if (r == 5) {
            char nomm[50];
            int aaan;
            printf("ANNEE: ");
            scanf("%d", &aaan);
            printf("NOM: ");
            scanf("%s", nomm);
            affiche_liste_vote(m, aaan, nomm);
        }
        else if (r == 4) {
            int aanne;
            printf("ANNEE: ");
            scanf("%d", &aanne);
            affiche_election(m, aanne);
        }
        else if (r == 3) {
            int annne;
            char nom[50], vote[50];
            printf("ANNEE: ");
            scanf("%d", &annne);
            printf("NOM: ");
            scanf("%s", nom);
            printf("VOTE: ");
            scanf("%s", vote);
            change_vote(m, annne, nom, vote);
            struct cellule* aux = m;
            while (aux->suiv != NULL) {
                struct election* la;
                if (aux->annee == annne) {
                    la = aux->val;
                    voteee(la);
                    printf("%s a ete electe\n", la->val.nom);
                }
                aux = aux->suiv;
            }
        }
        else if (r == 1) {
            char* noomm = (char*)malloc(50 * sizeof(char));
            char* votee = (char*)malloc(50 * sizeof(char));
            int anne;
            printf("NOM: ");
            scanf("%s", noomm);
            printf("VOTE: ");
            scanf("%s", votee);
            printf("ANNEE: ");
            scanf("%d", &anne);
            ajout_candidat(noomm, votee, anne, m);
            struct cellule* auux = m;
            while (auux->suiv != NULL) {
                struct election* la;
                if (auux->annee == anne) {
                    la = auux->val;
                    voteee(la);
                    printf("%s a ete electe\n", la->val.nom);
                }
                auux = auux->suiv;
            }
        }
        else if (r == 2) {
            int annee;
            printf("ANNEE: ");
            scanf("%d", &annee);
            struct election* la = creer_elec(annee);
            ajout_election(la, m);
            voteee(la);
            printf("%s a ete electe\n", la->val.nom);
        }
    }
    struct cellule* temp;
    while (m != NULL) {
        temp = m;
        m = m->suiv;
        free(temp);
    }

    return 0;
}
