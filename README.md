#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_USERS 100
#define MAX_NAME 50
#define MAX_FRIENDS 100

// Structure for each user
typedef struct {
    char name[MAX_NAME];
    int friends[MAX_FRIENDS];
    int friendCount;
} User;

User users[MAX_USERS];
int userCount = 0;

// Find user index
int findUser(char name[]) {
    for (int i = 0; i < userCount; i++) {
        if (strcmp(users[i].name, name) == 0)
            return i;
    }
    return -1;
}

// Add user
void addUser(char name[]) {
    if (findUser(name) != -1) {
        printf("User already exists.\n");
        return;
    }
    strcpy(users[userCount].name, name);
    users[userCount].friendCount = 0;
    userCount++;
    printf("User '%s' added.\n", name);
}

// Add friendship
void addFriendship(char u[], char v[]) {
    int i = findUser(u);
    int j = findUser(v);

    if (i == -1 || j == -1) {
        printf("User not found.\n");
        return;
    }

    users[i].friends[users[i].friendCount++] = j;
    users[j].friends[users[j].friendCount++] = i;

    printf("%s and %s are now friends.\n", u, v);
}

// BFS
void BFS(char start[]) {
    int visited[MAX_USERS] = {0};
    int queue[MAX_USERS], front = 0, rear = 0;

    int s = findUser(start);
    if (s == -1) return;

    visited[s] = 1;
    queue[rear++] = s;

    printf("BFS Traversal: ");
    while (front < rear) {
        int u = queue[front++];
        printf("%s ", users[u].name);

        for (int i = 0; i < users[u].friendCount; i++) {
            int v = users[u].friends[i];
            if (!visited[v]) {
                visited[v] = 1;
                queue[rear++] = v;
            }
        }
    }
    printf("\n");
}

// DFS (iterative)
void DFS(char start[]) {
    int visited[MAX_USERS] = {0};
    int stack[MAX_USERS], top = -1;

    int s = findUser(start);
    if (s == -1) return;

    stack[++top] = s;

    printf("DFS Traversal: ");
    while (top >= 0) {
        int u = stack[top--];

        if (!visited[u]) {
            visited[u] = 1;
            printf("%s ", users[u].name);

            for (int i = 0; i < users[u].friendCount; i++) {
                int v = users[u].friends[i];
                if (!visited[v])
                    stack[++top] = v;
            }
        }
    }
    printf("\n");
}

// Degrees of separation (BFS)
int degreesOfSeparation(char u[], char v[]) {
    int dist[MAX_USERS];
    for (int i = 0; i < MAX_USERS; i++) dist[i] = -1;

    int queue[MAX_USERS], front = 0, rear = 0;

    int start = findUser(u);
    int end = findUser(v);

    if (start == -1 || end == -1) return -1;

    dist[start] = 0;
    queue[rear++] = start;

    while (front < rear) {
        int cur = queue[front++];

        for (int i = 0; i < users[cur].friendCount; i++) {
            int next = users[cur].friends[i];
            if (dist[next] == -1) {
                dist[next] = dist[cur] + 1;
                queue[rear++] = next;
            }
        }
    }
    return dist[end];
}

// Mutual friends
void mutualFriends(char u[], char v[]) {
    int i = findUser(u);
    int j = findUser(v);

    if (i == -1 || j == -1) return;

    printf("Mutual friends: ");
    for (int x = 0; x < users[i].friendCount; x++) {
        int f1 = users[i].friends[x];
        for (int y = 0; y < users[j].friendCount; y++) {
            if (f1 == users[j].friends[y]) {
                printf("%s ", users[f1].name);
            }
        }
    }
    printf("\n");
}

// Display network
void displayNetwork() {
    printf("\n=== Social Network ===\n");
    for (int i = 0; i < userCount; i++) {
        printf("%s -> ", users[i].name);
        for (int j = 0; j < users[i].friendCount; j++) {
            printf("%s ", users[users[i].friends[j]].name);
        }
        printf("\n");
    }
}

// Main
int main() {
    addUser("Alice");
    addUser("Bob");
    addUser("Charlie");
    addUser("Diana");
    addUser("Eve");
    addUser("Frank");

    addFriendship("Alice", "Bob");
    addFriendship("Alice", "Charlie");
    addFriendship("Bob", "Diana");
    addFriendship("Charlie", "Diana");
    addFriendship("Diana", "Eve");
    addFriendship("Eve", "Frank");

    displayNetwork();

    BFS("Alice");
    DFS("Alice");

    printf("Degrees between Alice and Frank: %d\n",
           degreesOfSeparation("Alice", "Frank"));

    mutualFriends("Alice", "Diana");

    return 0;
}
