# Mermaid-view

flowchart LR
  %% Frontend Cluster
  subgraph Frontend Cluster
    direction TB
    FE[Web Client]
    MOB[Mobile App]
  end

  %% API/Gateway Cluster
  subgraph API Cluster
    direction TB
    BFF[API Gateway / BFF]
  end

  %% Business Logic Cluster (каждый кластер с фасадом)
  subgraph Services Cluster
    direction TB
    subgraph Product Cluster
      PF[ProductFacade]
      %% внутри: ProductReader, ProductValidator, ProductFormatter
    end
    subgraph Order Cluster
      OF[OrderFacade]
      %% внутри: OrderReader, OrderCalculator, OrderNotifier
    end
    subgraph User Cluster
      UF[UserFacade]
      %% внутри: UserReader, UserAuth, UserProfileManager
    end
    subgraph Payment Cluster
      PayF[PaymentFacade]
      %% внутри: PaymentProcessor, PaymentValidator, PaymentLogger
    end
  end

  %% Data Cluster
  subgraph Data Cluster
    direction LR
    DB[(Primary Database)]
    Cache[(In-memory Cache)]
  end

  %% External Services
  subgraph External Services
    direction LR
    PayG[Payment Gateway]
    ShipS[Shipping Service]
  end

  %% Связи
  FE -->|HTTP/HTTPS| BFF
  MOB -->|HTTP/HTTPS| BFF

  BFF -->|REST/gRPC| PF
  BFF -->|REST/gRPC| OF
  BFF -->|REST/gRPC| UF
  BFF -->|REST/gRPC| PayF

  PF -->|SQL/ORM| DB
  OF -->|SQL/ORM| DB
  UF -->|SQL/ORM| DB
  PayF -->|SQL/ORM| DB

  PF -->|get/set| Cache
  OF -->|get/set| Cache

  PayF -->|API| PayG
  OF -->|API| ShipS
