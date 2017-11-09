| Resurs | Basic | Standard | Premium |
|---|---|---|---|---|
| Lagring | 10 giB | 100 giB| 500 giB |
| ReadOps per minut<sup>1, 2</sup> | 1 kB | 300 kB | 10 000 k |
| WriteOps per minut<sup>1, 3</sup> | 100 | 500 | 2k |
| Hämta bandbredd Mbit/s<sup>1</sup> | 30 | 60 | 100 |
| Överför bandbredd Mbit/s<sup>1</sup> | 10 | 20 | 50 |
| Webhooks | 2 | 10 | 100 |
| Geo-replikering | Saknas | Saknas | [Stöd för *(förhandsgranskning)*](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication) |

<sup>1</sup> *ReadOps*, *WriteOps*, och *bandbredd* är minsta uppskattningar. ACR strävar efter att förbättra prestanda som kräver användning.

<sup>2</sup> [docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) innebär att flera läsåtgärder baserat på antalet lager i bilden plus hämtning av manifestet.

<sup>3</sup> [docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image) innebär att flera skrivåtgärder, baserat på antalet lager som måste skickas. En `docker push` innehåller *ReadOps* att hämta ett manifest för en befintlig avbildning.