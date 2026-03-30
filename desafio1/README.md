# Desafio 1 — Imagem Docker Go < 2MB

Desafio da pós-graduação **Full Cycle**: criar uma imagem Docker com Go que exiba `Full Cycle rocks` ao rodar o container, com tamanho **menor que 2MB**.

---

## Estrutura

```
desafio1/
├── main.go       # Programa Go que imprime a mensagem
└── Dockerfile    # Multi-stage build: builder (golang:alpine) + final (scratch)
```

---

## Como funciona

A imagem usa **multi-stage build** com dois estágios:

### Stage 1 — Build (`golang:1.22-alpine`)
Compila o programa Go em um binário estático com as seguintes flags:

| Flag | Motivo |
|---|---|
| `CGO_ENABLED=0` | Desativa CGO, gerando um binário 100% estático |
| `GOOS=linux` | Garante compatibilidade com Linux |
| `-ldflags "-s -w"` | Remove símbolos e debug, reduzindo o tamanho do binário |

### Stage 2 — Final (`scratch`)
`scratch` é uma imagem **completamente vazia** — sem sistema operacional, sem shell, sem nada. Funciona porque o binário Go compilado com as flags acima é totalmente auto-suficiente.

```
[golang:alpine ~300MB]       [scratch]
┌──────────────────┐         ┌──────────┐
│  go build → /app │  ──►    │   /app   │  ← só o binário!
└──────────────────┘         └──────────┘
                                < 2MB
```

---

## Como usar

**Build da imagem:**
```bash
docker build -t fullcycle-go .
```

**Rodar o container:**
```bash
docker run --rm fullcycle-go
```

**Saída esperada:**
```
Full Cycle rocks
```

**Verificar o tamanho:**
```bash
docker images fullcycle-go
```

---

## Resultado

```
REPOSITORY     TAG       SIZE
fullcycle-go   latest    ~1.8MB
```

Imagem abaixo de 2MB. ✓
