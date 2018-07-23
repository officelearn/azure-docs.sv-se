| Resurs | Standardgräns |
| --- | :--- |
| Maximalt antal noder per kluster | 100 |
| Maximalt antal poddar per nod ([grundläggande nätverk med Kubenet][basic-networking]) | 110 |
| Maximalt antal poddar per nod ([avancerade nätverk med Azure CNI][advanced-networking]) | 30<sup>1</sup> |
| Maximalt antal kluster per prenumeration | 100 |

<sup>1</sup> Det här värdet kan anpassas med hjälp av ARM-malldistribution. Exempel finns [här][arm-deployment-example].<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/networking-overview.md#basic-networking
[advanced-networking]: ../articles/aks/networking-overview.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[arm-deployment-example]: https://github.com/Azure/AKS/blob/master/examples/vnet/02-aks-custom-vnet.json#L64-L69
