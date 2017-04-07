# Trabalho-Teoria-de-grafos
Aluno: Victor Oliveira Silva Nunes RA: C40BGG2

Implementação do algoritmo de Dijkstra

Foi implementado o algoritmo de Dijkstra no contexto de um resort que utiliza um sistema para facilitar a locomoção dos turistas dentro do seu resort

Para cada local foi designado um código para facilitar a utilização do sistema:
O mapeamento do grafo esta em Issues com seus nomes, codigos e pesos

Portaria = 1                Padaria = 5                  Parque ecológico = 9
Parque Aquático = 2    Praça da lua = 6          Área Residencial = 10
Parquinho = 3              Sorveteria = 7             Restaurante = 11
Praça do sol = 4          Praça do mirante = 8    Parque de diversão = 12

O usuário define sua localidade atual e seu desejo de destino em sequencia programa usará o algoritmo de Diijktra para encontra a melhor rota entre as duas localidades definidas pelo usuário.

*O codigo fonte foi dividido em 2 classes: Main e Graph

CODIGO FONTE:
Classe fixa Graph com suas explicações.

package teoriadegrafos;

import java.util.*;

    // Classe onde está os metodos para a busca e criação do grafo
    public class Graph {
    //Variavel utilizada para deixar os valores dos vertices antecessores indefinidos
    private static final int UNDEFINED = -1;
    private int vertices[][];
    // construtor
    public Graph(int numVertices) {
        vertices = new int[numVertices][numVertices];
    }
    // Metodo para a obtenção dos pesos entre vertice 1 e vertice 2
    // Metodo utilizado na classe Main Ex: hotel.makeEdge(vertice1, vertice2, peso)
    public void makeEdge(int vertex1, int vertex2, int time) {
        vertices[vertex1][vertex2] = time;
        vertices[vertex2][vertex1] = time;
    }
    
    //metodo utilizado para pegar o custo de um caminho entre dois vertices
    public int getCost(int vertex1, int vertex2) {
        // retorna o custo do dos dois vertices que foram passados por parametro no metodo
        return vertices[vertex1][vertex2];
    }

    //Retona uma lista com os valores de todos os vertices conectados com o vertice dado.
    // essa lista é utilizada para saber qual sera o proximo vertice a ser processado. 
    public List<Integer> getNeighbors(int vertex) {
        List<Integer> neighbors = new ArrayList<>();
        for (int i = 0; i < vertices[vertex].length; i++)
            if (vertices[vertex][i] > 0) {
                neighbors.add(i);
            }

        return neighbors;
    }

    /**
     * Aqui é a implementação do algoritmo de Diijkstra
     * O O parametro from é o vertice de entrada
     * E o parametro to é o vertice de destino
     * No final é retornado o caminho entre o vertice de entrada (from) e destino (to).
     */
    public List<Integer> path(int from, int to) {
        //Inicialização 
        int cost[] = new int[vertices.length];
        int prev[] = new int[vertices.length];
        Set<Integer> unvisited = new HashSet<>();

        //O vertice de inicio não tem custo e nem tem um vertice antecessor
        cost[from] = 0;

        //Define todos os outros vertices com peso infinito e seu antecessor indefinido por em quanto.
        //Conforme o algoritmo vai rodando ele vai setando os custo e definindo seus vertices antecessores.
        for (int v = 0; v < vertices.length; v++) {
            if (v != from) {
                cost[v] = Integer.MAX_VALUE;
            }
            prev[v] = UNDEFINED;
            unvisited.add(v);
        }

        //Aqui é conde o codigo fará a pesquisa dos grafos 
        
        // enquanto a lista de vertices NÂO visitados não estiver vazia ela continua o loop.
        while (!unvisited.isEmpty()) { 
            
            int near = closest(cost, unvisited);
            unvisited.remove(near);
            // pega os vertices vizinhos 
            for (Integer neighbor : getNeighbors(near)) {
                //calcula o custo dos vertices obtidos
                int totalCost = cost[near] + getCost(near, neighbor);
                if (totalCost < cost[neighbor]) {
                    cost[neighbor] = totalCost;
                    prev[neighbor] = near;
                }
            }
            //Se o vertice onde está atualmente é o vertice destino... 
            if (near == to) {
                // retorne a lista do caminho do vertice de entrada e destino.
                // o metodo makePathList esta escrito no fim do codigo
                return makePathList(prev, near);
            }
        }

        //Se não foi possivel encontrar nenhum caminha retone uma lista vazia 
        return Collections.emptyList();
    }
    // metodo utilizado para encontrar o vertice que esta mais perto para ser o proximo a processar.
    private int closest(int[] dist, Set<Integer> unvisited) {
        double minDist = Integer.MAX_VALUE;
        int minIndex = 0;
        for (Integer i : unvisited) {
            if (dist[i] < minDist) {
                minDist = dist[i];
                minIndex = i;
            }
        }
        
        return minIndex;
    }
    // metodo utilizado para fazer a lista do caminho quando for encontrado 
    private List<Integer> makePathList(int[] prev, int u) {
        List<Integer> path = new ArrayList<>();
        path.add(u);
        while (prev[u] != UNDEFINED) {
            path.add(prev[u]);
            u = prev[u];
        }
        Collections.reverse(path);
        return path;
    }
}

 Classe Main:
 package teoriadegrafos;

import java.util.Scanner;

public class Main {
    // Criação dos vertices
    
    private static final int Portaria = 0;
    private static final int ParqueAquatico = 1;
    private static final int Parquinho = 2;
    private static final int PracaDoSol = 3;
    private static final int Padaria = 4;
    private static final int PracaDaLua = 5;
    private static final int Sorveteria = 6;
    private static final int PracaDoMirante = 7;
    private static final int ParqueEcologico = 8;
    private static final int AreaResidencial = 9;
    private static final int Restaurante = 10;
    private static final int ParqueDeDiversao = 11;
    
    //Apenas um metodo para a leitura do que foi digitado pelo usuario.
    private static int leCodigoDoLocal(String type, Scanner in) {
        while (true) {
            System.out.println(type + ":");
            String line = in.nextLine().trim();
            if (line.isEmpty()) {
                System.out.println("Até mais!");
                System.exit(0);
            }
            try {
                int local = Integer.parseInt(line);
                if (local >= 1 && local <= 20) return local-1;
            } catch (NumberFormatException e) {
            }
            System.out.println("Codigo do local invalido! Cheque a lista dos locais e seus codigos.");
        }
    }

    // Metodo Main
    public static void main(String[] args) {
        
        Graph resort = new Graph(20);

        //Criação das arestas e seus pesos 
        //             Vertice 1    Vertice 2       Peso entre Vertice 1 e Vertice      
        resort.makeEdge(Portaria,    ParqueAquatico, 10); 
        resort.makeEdge(ParqueAquatico, Parquinho, 4);
        resort.makeEdge(ParqueAquatico, PracaDoSol, 3);
        resort.makeEdge(ParqueAquatico, Padaria, 2);
        resort.makeEdge(Parquinho, PracaDoSol, 11);
        resort.makeEdge(Parquinho, PracaDoMirante, 9);
        resort.makeEdge(PracaDoMirante, AreaResidencial, 2);
        resort.makeEdge(PracaDoMirante, ParqueEcologico, 1);
        resort.makeEdge(ParqueEcologico, AreaResidencial, 13);
        resort.makeEdge(PracaDoMirante, PracaDoSol, 16);
        resort.makeEdge(AreaResidencial, Restaurante, 5);
        resort.makeEdge(PracaDoSol, Restaurante, 9);
        resort.makeEdge(PracaDoSol, Padaria, 3);
        resort.makeEdge(Restaurante, PracaDaLua, 8);
        resort.makeEdge(Restaurante, ParqueDeDiversao, 7);
        resort.makeEdge(PracaDaLua, ParqueDeDiversao, 6);
        resort.makeEdge(Padaria, Sorveteria, 1);
        resort.makeEdge(Sorveteria, ParqueDeDiversao, 4);
        resort.makeEdge(PracaDaLua, Sorveteria, 3);
        resort.makeEdge(PracaDaLua, Padaria, 5);
        

        Scanner in = new Scanner(System.in);
        System.out.println("Seja bem vindo ao resort!");
        System.out.println("Veja a lista dos códigos das localidades e coloque seu ponto atual e o ponto onde deseja chegar! ");
        //Lista de codigo para cada local do hotel
        System.out.println("Portaria = 1, Parque Aquático = 2, Parquinho = 3, Praça do sol = 4");
        System.out.println("Padaria = 5, Praça da lua = 6, Sorveteria = 7, Praça do mirante = 8");
        System.out.println("Parque ecológico = 9, Área residêncial = 10, Restaurante  = 11, Parque De Diversão = 12");
        

        while (true) {
            System.out.println("Coloque o código do seu ponto inicial e final");
            int source = leCodigoDoLocal("Código do local atual", in);
            int destination = leCodigoDoLocal("Código do destino", in);

            System.out.println("Caminho mais rapido:");
            //Loop para a criação da escritura do caminho para o usuario.
            for (Integer local : resort.path(source, destination)) {
                System.out.print((local+1) + " -> ");
            }

        }
    }
}
