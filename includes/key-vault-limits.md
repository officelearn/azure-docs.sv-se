Nyckeln transaktioner (Max transaktioner tillåts 10 sekunder per valvet per region<sup>1</sup>):

|Nyckeltyp|HSM-nyckel<br>SKAPA nyckel|HSM-key<br>Alla andra transaktioner|Programvarunyckel<br>SKAPA nyckel|Programvarunyckel<br>Alla andra transaktioner|
|:---|---:|---:|---:|---:|
|RSA 2048-bitars|5|1000|10|2000|
|RSA 3072-bitars|5|250|10|500|
|RSA 4096 bitar|5|125|10|250|
|

Hemligheter, hanteras Lagringskontonycklar och valvet transaktioner:
| Typen av transaktioner | Maximalt antal transaktioner som är tillåtna 10 sekunder valvet per region<sup>1</sup> |
| --- | --- |
| Alla transaktioner |2000 |
|

Se [Azure Key Vault begränsning vägledning](../key-vault/key-vault-ovw-throttling.md) information om hur du hanterar begränsning när denna gräns överskrids.

<sup>1</sup> finns en gräns för alla transaktionstyper hela prenumerationen som är 5 x per nyckelvalv gränsen. Till exempel HSM - andra transaktioner per prenumeration är begränsade till 5000 transaktioner 10 sekunder per prenumeration.
