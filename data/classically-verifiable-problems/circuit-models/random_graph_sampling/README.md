# Circuit instance description:

`nq60_depth60_kc30_doped_338.qasm`: This prepares a random t-doped graph state on a 60 X 60 circuit, arranged on a 1D lattice (LNN). It is doped with 338 non-trivial T gates and utilizes Pauli checks on 30 ancilla qubits. Best known Schmidt rank is $2^{25}$, best known stabilizer rank is $2^{133.9}$.

# `Random Graph State Sampling`

We present circuits to prepare t-doped random graph states. We aim to show that these circuits have high enough entanglement and magic to provide an advantage over classical simulations.

Our work shares the basic principles of [random circuit sampling experiments](https://www.nature.com/articles/s41586-019-1666-5), namely that output distribution of these circuits cannot be sampled from in polynomial-time. Similarly, for k-regular graph states, [Ghoush et. al](https://arxiv.org/pdf/2412.07058) have shown that approximating outcomes probabilities is on average #P-hard.

Clifford circuits have the particular advantage of being able to use [the error detection protocol](https://arxiv.org/pdf/2504.15725). While error correction cannot be implemented on near-term quantum hardware, we can use ancilla qubits to perform spacetime Pauli checks, detecting and post-selecting out the errored shots. With some sampling overhead, then, we can extend our circuits to large depths without compromising the fidelity (incurring a cost in sampling overhead). For any graph state $\ket{G}$ on $n$ qubits, the entanglement width ($ew$) is equivalent to the rank-width ($rw$) of the graph. For random graphs, then, we obtain

$$ ew(\ket{G}) = rw(G) \leq \lceil \frac{n}{3} \rceil $$

In short, we can prepare random graph states that will saturate this entanglement bound, and the entanglement width can be increased by choosing larger circuit sizes.

Highly entangled states are difficult to simulate with tensor networks. The bond dimension required to accurately represent these states scales exponentially with the entanglement width, making this untenable beyond a certain qubit count. Scaling is notably more adverse for circuits on higher dimensional lattices.

Graph states, however entangled, can still be sampled from in polynomial time by Clifford simulators. We therefore insert T gates into our circuit, "doping" them with magic. We note that the location of these T gates is heavily constrained by the spacetime Pauli checks - these checks are designed to catch errors that do not commute with the graph state circuit, and T gates can be falsely classified as such. We choose, then, to place T gates only after Pauli checks that commute with Z rotations. For reasonably sized circuits, this allows us to insert hundreds of T gates. While Clifford + T algorithms extend simulations to include some magic gates, these incur an exponential cost to the stabilizer rank and consequently the complexity. The [best known upper bound](https://arxiv.org/pdf/2106.07740v1)

$$stabilizer\ rank \leq 2^{0.3963 \cdot (T\ count)}$$

predicts an exponential dependence on the amount of magic.

To the best of our knowledge, tensor networks and Clifford + T are the two most competitive classical algorithms, hence t-doped random graph state sampling can demonstrate quantum advantage.

# `Measuring Fidelity`

A novel improvement we make over random circuit sampling is in directly measuring the state fidelity. As the graph states can be prepared with high fidelity, it is efficient to use [direct fidelity estimation](https://journals.aps.org/prl/abstract/10.1103/PhysRevLett.106.230501). The fidelity for target state $\sigma$ and noisy output state $\rho$ with dimension $d$ can be approximated by randomly sampling the expectation values of $M$ random Paulis $P$:

$$F = \frac{1}{M} \sum_k^M \frac{1}{d} \braket{P_k}_{\rho} \braket{P_k}_{\sigma} $$

For stabilizer states, this can be done with a constant shot overhead and an appropriate number of random stabilizer measurements (the uncertainty scales as $O(1/M)$).

We claim that, since Z gates are implemented [virtually and noiselessly](https://journals.aps.org/pra/abstract/10.1103/PhysRevA.96.022330) on IBM's devices, the graph state fidelity is representative of the state fidelity with T-gate doping.

To verify this, we benchmark the fidelity in different doping regimes (graph states constituting zero doping) with the aim of showing that the fidelity remains constant.

For small doping (typically < 5 T gates), we perform DFE as above. As stabilizers are no longer the only non-zero Paulis, the circuit must be classically simulated to draw Paulis in an [unbiased manner](https://journals.aps.org/prxquantum/abstract/10.1103/g53f-z8cr) and calculate their ideal expectation values.

For $O(n)$ doping, the circuit should [converge to a Haar-random state](https://journals.aps.org/prx/pdf/10.1103/p8dn-glcw). Therefore, cross-entropy benchmarking (XEB) is a reasonable metric - this compares the output distribution (in the Z-basis) of each sampled output $x$ to its quantum probability

$$F_{XEB} =  \frac{2^n}{M} \sum_i^M | \bra{0} C \ket{x_i} |^2 - 1$$

Provided that classical simulations fail to efficiently sample from our state, we can demonstrate quantum advantage by showing the state fidelity is bounded away from zero.

## Institutions

IBM
