[![](https://mermaid.ink/img/pako:eNptkMtuwyAQRX8FzTr5AS8q-ZFk00qV3R10MTWTBNWAxaNSFeXfS-xJFVVlgThcYA5zgdFrggpOAeezeO6VE2XUsqeRzBeJVx_S-7rZyNa7mEIek3ihGPFEnLTyLaCLRx_sn6S73dEmGe9wEsM8mftrO9n7nEjUzHvmhvkgB3L6sf46x_zBrt7PLCa22yfRsssC3Qrlhce79ZI1rFZA_Npxtvs3WwvsWXyBA1vfATZgKVg0ujTzcgsVpDNZUlCVpcbwqUC5azmHOfnh241QlV7SBvKsMVFnsPzLQnXEKdL1B9r3eS0?type=png)](https://mermaid.live/edit#pako:eNptkMtuwyAQRX8FzTr5AS8q-ZFk00qV3R10MTWTBNWAxaNSFeXfS-xJFVVlgThcYA5zgdFrggpOAeezeO6VE2XUsqeRzBeJVx_S-7rZyNa7mEIek3ihGPFEnLTyLaCLRx_sn6S73dEmGe9wEsM8mftrO9n7nEjUzHvmhvkgB3L6sf46x_zBrt7PLCa22yfRsssC3Qrlhce79ZI1rFZA_Npxtvs3WwvsWXyBA1vfATZgKVg0ujTzcgsVpDNZUlCVpcbwqUC5azmHOfnh241QlV7SBvKsMVFnsPzLQnXEKdL1B9r3eS0)

---

graph LR
    A[Receive Port]
    B[Construct Message]
    C[Transform Message]
    D[Conditional Split]
    E[Route A]
    F[Route B]
    G[Send Port]
    
    subgraph Loop
    B --> C
    C --> D
    end
    
    A --> B
    D -- Condition A --> E
    D -- Condition B --> F
    E --> G
    F --> G
