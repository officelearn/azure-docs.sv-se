| Resurs | Standardgräns |
| --- | --- |
| Antal lagringskonton per prenumeration | 200<sup>1</sup> |
| Maximal kapacitet för lagringskonton | 500 TiB<sup>2</sup> |
| Högsta antal blob-behållare, blobbar, filresurser, tabeller, köer, entiteter eller meddelanden per storage-konto | Obegränsat |
| Maximal förfrågningar per lagringskonto | 20 000 begäranden per sekund<sup>2</sup> |
| Max ingång<sup>3</sup> per lagringskonto (oss regioner) | 10 Gbit/s om GRS/ZRS<sup>4</sup> aktiverad, 20 Gbit/s för LRS<sup>2</sup> |
| Maximalt antal utgående<sup>3</sup> per lagringskonto (oss regioner) | 20 Gbit/s om RA-GRS/GRS/ZRS<sup>4</sup> aktiverad, 30 Gbit/s för LRS<sup>2</sup> |
| Max ingång<sup>3</sup> per lagringskonto (icke-amerikansk regioner) | 5 Gbit/s om GRS/ZRS<sup>4</sup> aktiverad, 10 Gbit/s för LRS<sup>2</sup> |
| Maximalt antal utgående<sup>3</sup> per lagringskonto (icke-amerikansk regioner) | 10 Gbit/s om RA-GRS/GRS/ZRS<sup>4</sup> aktiverad, 15 Gbit/s för LRS<sup>2</sup> |

<sup>1</sup>innehåller både Standard- och Premium storage-konton. Om du behöver mer än 200 lagringskonton skickar du en begäran via [Azure-supporten](https://azure.microsoft.com/support/faq/). Azure Storage-teamet granskar ditt affärsfall och kan godkänna upp till 250 lagringskonton. 

<sup>2</sup> för att få dina lagringskonton som standard att växa annonserade gränser i kapacitet, ingång-/ utgång och begäran som gör en begäran via [Azure-supporten](https://azure.microsoft.com/support/faq/). Azure Storage-teamet kommer granska förfrågningen och godkänna högre gränser på en fall till fall.

<sup>3</sup> begränsad endast av kontots ingång-/ utgång gränser. *Ingång* refererar till alla data (antal begäranden) som skickas till ett lagringskonto. *Utgående* syftar på alla data (svar) som tas emot från ett lagringskonto.  

<sup>4</sup>alternativ för azure Storage-redundans inkluderar:
* **RA-GRS**: geo-redundant lagring med läsbehörighet. Utgående mål för den sekundära platsen är samma som för den primära platsen om RA-GRS är aktiverad.
* **GRS**: Geo-redundant lagring. 
* **ZRS**: zonredundant lagring. Endast tillgängligt för blockblobbar. 
* **LRS**: lokalt redundant lagring. 