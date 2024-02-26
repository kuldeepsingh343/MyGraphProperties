# MyGraphProperties
Homework

// Exercise 4.1.16
package algs41;

import java.util.*;
import stdlib.*;


// This is problem 4.1.16 from the textbook
//
// You need only change the constructor.
// You can also change the main method.
// But you should not change eccentricity(), diameter(), radius(), center() or isCenter()
// You can (and should!) add more private methods (such as bfs or dfs)
//
// TODO: complete the following, using only Graph and GraphGenerator.
// You may copy code from other classes in algs41, but you should not use the classes themselves.  
// In particular, you may not use BreadthFirstPaths although you may copy code from there.
//
// The "distance" from vertex v to vertex w is the length of the shortest path
// from v to w.
// The "eccentricity" of a vertex v is distance to the farthest vertex from v.
// The "diameter" of a graph is the maximum eccentricity of any vertex. 
// The "radius" of a graph is the smallest eccentricity of any vertex. 
// A "center" is a vertex whose eccentricity is the radius.
// The center is not necessarily unique.

public class MyGraphProperties {
	int[] eccentricity;
	int diameter;
	int radius;
	int center;

	// Constructor can be linear in G.V() * G.E()
	public MyGraphProperties(Graph G) {
		this.eccentricity = new int[G.V()];
		int diameter = Integer.MIN_VALUE;
		int radius = Integer.MAX_VALUE;
		int center = -1;
		// If G.V()==0, then these are the correct values.
		// If G is not connected, you should throw a new IllegalArgumentException()
		// I suggest that you first get it to work for a connected graph
		// This will require that you traverse the graph starting from some node (say 0)
		// You can then adjust your code so that it throws an exception in the case that all nodes are not visited

		// TODO

		for (int v = 0; v < G.V; v++) {
			eccentricity[v] = bfs(G,v);
			if(eccentricity[v] == Integer.MAX_VALUE)
				throw new IllegalArgumentException();
			if(diameter < eccentricity[v])
				diameter = eccentricity[v];
			if(radius > eccentricity[v]) {
				radius = eccentricity[v];
				center = v;
			}
		}

		// compute the eccentricity, diameter, radius and center
		// The center of the graph is not unique, set center to SOME center --- it does not matter which one
		this.diameter = diameter;
		this.radius   = radius;
		this.center   = center;
	}
	private class Graph {
		int V;
		LinkedList<Integer> adj[];

		// constructor
		Graph(int V)
		{
			this.V = V;

			// define the size of array as
			// number of vertices
			adj = new LinkedList[V];

			// Create a new list for each vertex
			// such that adjacent nodes can be stored
			for(int i = 0; i < V; i++) {
				adj[i] = new LinkedList<>();
			}
		}
	}

	private int bfs(Graph G, int s) {
		Queue<Integer> q = new LinkedList<>();
		int distTo[] = new int[G.V];
		for (int v = 0; v < G.V; v++) distTo[v] = Integer.MAX_VALUE;
		distTo[s] = 0;
		boolean marked[] = new boolean[G.V];
		marked[s] = true;
		q.add(s);
		int max = 0;
		while (!q.isEmpty()) {
			int v = q.remove();
			for (int w : G.adj[v]) {
				if (!marked[w]) {
					distTo[w] = distTo[v] + 1;
					marked[w] = true;
					max = distTo[w];
					q.add(w);
				}
			}
		}
		return max;
	}


	// Do not change the following constant time methods
	public int eccentricity(int v) { return eccentricity[v]; }
	public int diameter()          { return diameter; }
	public int radius()            { return radius; }
	public int center()            { return center; }
	public boolean isCenter(int v) { return eccentricity[v] == radius; }

	public static void main(String[] args) {
		//Graph G = GraphGenerator.fromIn (new In("data/tinyG.txt")); // this is non-connected -- should throw an exception
		//Graph G = GraphGenerator.connected (10, 20, 2); // Random non-connected graph -- should throw an exception

		Graph G = GraphGenerator.fromIn (new In("data/tinyCG.txt")); // diameter=2, radius=2, every node is a center
		//Graph G = GraphGenerator.binaryTree (10); // A complete binary tree
		//Graph G = GraphGenerator.path (10); // A path -- diameter=V-1
		//Graph G = GraphGenerator.connected (20, 400); // Random connected graph

		StdOut.println(G);
		G.toGraphviz ("g.png");

		MyGraphProperties gp = new MyGraphProperties(G);
		for (int v = 0; v < G.V(); v++)
			StdOut.format ("eccentricity of %d: %d\n", v, gp.eccentricity (v));
		StdOut.format ("diameter=%d, radius=%d, center=%d\n", gp.diameter(), gp.radius (), gp.center ());
	}
}
