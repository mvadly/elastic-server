# Elastic Stack — Docker

Elastic Stack **7.17.17** (Elasticsearch, Kibana, APM Server) menggunakan Podman/Docker Compose.

## Prasyarat

- [Podman](https://podman.io) atau [Docker](https://docker.com)
- `podman-compose` atau `docker-compose`
- Linux: set `vm.max_map_count` untuk Elasticsearch:

```bash
sudo sysctl -w vm.max_map_count=262144
```

## Struktur Direktori

```
elastic/
├── docker-compose.yaml
├── elasticsearch/
│   ├── config/elasticsearch.yml
│   └── data/
├── apm-server/
│   ├── apm-server.yml
│   └── data/
└── kibana/
    ├── config/kibana.yml
    └── data/
```

## Cara Menjalankan

```bash
podman-compose up -d
# atau: docker compose up -d
```

### Akses

| Service         | URL                         |
|-----------------|-----------------------------|
| Kibana          | http://localhost:5601       |
| Elasticsearch   | http://localhost:9200       |
| APM Server      | http://localhost:8200       |

## Layanan

### Elasticsearch
- Mode **single-node**, tanpa autentikasi (xpack.security disabled)
- Data persisten di `./elasticsearch/data/`
- Health check otomatis: `GET /_cluster/health`

### Kibana
- Konfigurasi: `./kibana/config/kibana.yml`
- Tidak perlu login (security disabled)
- Saved objects terenkripsi dengan key dari config

### APM Server
- Konfigurasi: `./apm-server/apm-server.yml`
- Menerima data dari APM agents di port **8200**
- Output dikirim ke Elasticsearch di `http://elasticsearch:9200`

## Catatan

- **Disk watermarks** disetel 99% untuk mengakomodasi WSL dengan filesystem 9p (disk D: yang hampir penuh)
- APM Server menggunakan `--strict.perms=false` karena keterbatasan permission di filesystem 9p/WSL
- Semua data service ter-mount ke direktori lokal untuk persistensi
- Untuk menghentikan: `podman-compose down` atau `docker compose down`
