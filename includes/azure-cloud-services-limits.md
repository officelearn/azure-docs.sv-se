| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| [Web/worker-roller per distribution](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [Instansen indata slutpunkter](http://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint) per distribution |25 |25 |
| [Ange slutpunkter](http://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint) per distribution |25 |25 |
| [Interna slutpunkter](http://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint) per distribution |25 |25 |

<sup>1</sup>varje molntjänst med Web/Worker-roller kan ha två distributioner, en för produktion och en för Förproduktion. Tänk också på att den här gränsen avser antalet distinkta roller (konfiguration) och inte antalet instanser per roll (skalning).

