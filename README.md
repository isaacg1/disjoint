# disjoint

This crate provides `DisjointSet`, a [disjoint-set data structure] implemented in 100% safe Rust.

## Examples

Disjoint set data structures can be applied to find the [minimal spanning forest] of an [undirected edge-weighted graph]. Let's assume we work with the following graph interface:

```rust
    trait Edge : Copy {
        fn first_vertex(&self) -> usize;
        fn second_vertex(&self) -> usize;
    }
    
    trait Graph {
        type E : Edge;
        fn edges_ordered_by_weight(&self) -> Vec<Self::E>;
        fn number_vertices(&self) -> usize;
        fn new(edges: Vec<Self::E>) -> Self;
    }
```

Then it's very straight-forward to use the provided [`DisjointSet`] struct and its methods `is_joined` and `join` to implement [Kruskal’s algorithm] to find the minimum spanning forest.

```rust
use disjoint::DisjointSet;

fn minimum_spanning_forest<G : Graph>(graph: &G) -> G {
    let mut result_edges = Vec::new();
    let mut vertices = DisjointSet::new(graph.number_vertices());

    for edge in graph.edges_ordered_by_weight() {
        if !vertices.is_joined(edge.first_vertex(), edge.second_vertex()) {
            vertices.join(edge.first_vertex(), edge.second_vertex());
            result_edges.push(edge);
        }
    }
    
    Graph::new(result_edges)
}
```

We can even use the fact that `join` returns `true` if the elements have not been joined already, to further simplify the algorithm (this variation is cometimes called Quick-Union):
  
```rust
use disjoint::DisjointSet;

fn minimum_spanning_forest_quick_find<G : Graph>(graph: &G) -> G {
    let mut result_edges = Vec::new();
    let mut vertices = DisjointSet::new(graph.number_vertices());

    for edge in graph.edges_ordered_by_weight() {
        if vertices.join(edge.first_vertex(), edge.second_vertex()) {
            result_edges.push(edge);
        }
    }
    
    Graph::new(result_edges)
}
```

See the [documentation] for more details on how these and other methods work.

[disjoint-set data structure]: https://en.wikipedia.org/wiki/Disjoint-set_data_structure
[undirected edge-weighted graph]: https://en.wikipedia.org/wiki/Graph_(discrete_mathematics)#Weighted_graph
[minimal spanning forest]: https://en.wikipedia.org/wiki/Minimum_spanning_tree
[Kruskal’s algorithm]: https://en.wikipedia.org/wiki/Kruskal%27s_algorithm
[documentation]: https://docs.rs/disjoint/latest/disjoint/struct.DisjointSet.html

## License

Licensed under either of:

 * Apache License, Version 2.0, ([LICENSE-APACHE](LICENSE-APACHE) or https://www.apache.org/licenses/LICENSE-2.0)
 * MIT license ([LICENSE-MIT](LICENSE-MIT) or https://opensource.org/licenses/MIT)

at your option.

### Contribution

Unless you explicitly state otherwise, any contribution intentionally submitted
for inclusion in the work by you, as defined in the Apache-2.0 license, shall be dual licensed as above, without any
additional terms or conditions.