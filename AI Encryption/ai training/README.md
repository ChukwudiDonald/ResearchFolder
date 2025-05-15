## 1  Motivation
Standard ASCII uses a one‑to‑one mapping—each character maps to a unique integer (e.g., `a` → `97`).  
This project introduces a non‑linear, **many‑to‑one** table in which several symbols share the same code‑point.

## 2  Example Cluster Mapping

| Code | Cluster Members |
|------|-----------------|
| 97   | a, x, y |
| 113  | t, q, b |
| 101  | e, z, v |
| …    | … |

## 3  Ciphertext Example
Plaintext **“the boy”** can be compressed into ambiguous ciphertext such as:

```
bhz qgx
```
or
```
qhv toa
```

This is possible because of overlap—e.g., `[tqb]h[ezv] [tqb][ojg][axy]` still resolves to **“the boy”**.

## 4  Position‑Dependent Interpretation
In a large text, the same code‑point can decode differently depending on context:  
`97` may expand to **“a”** on line 1 but to **“y”** on line 50.

## 5  Training Procedure
We trained a single sequence‑to‑sequence **GRU** model that expands cluster tokens to plaintext characters.

* **Architecture:** 2‑layer bidirectional GRU with attention.

**Training stages**

* Pre‑training on **200 k** word‑level samples (overlap clusters) → **94 %** validation accuracy.  
* Fine‑tuning on **800 k** phrase‑level samples → **99.05 %** overall accuracy.

**Datasets:** *BookCorpus* and `words_alpha.txt`.

**Sample training pair (CSV format)**

```csv
input,target,original
[tqb][ezv][ezv]n [axy][tqb]l[ezv] [tqb][ojg] r[ezv]s[ikp]s[tqb] [tqb]u[axy][ikp]n[ojg] [axy] f[ezv]w,b,been able to resist buying a few
```

During training, the model is asked to predict the earliest uncertain symbol and gradually assemble the full sentence.

> 

## 6  Next Steps
* Scale to longer sentences and larger symbol sets.  
* Integrate production‑grade security by combining the Neural Cypher with AES.  
* Treat the overlapping mapping itself as part of the cryptographic key.  

---

*— End of Concept Note —*
