



Vi har skapat konceptet Azure-beräkningsenheter (ACU, Azure Compute Unit) för att göra det lättare att jämföra beräkningsprestanda (CPU) mellan Azure SKU:er. På så sätt blir det lättare att identifiera vilken SKU som bäst uppfyller dina behov.  ACU är för närvarande standardiserat på en liten virtuell dator (Standard_A1) och är 100, och alla andra SKU:er representerar ungefär hur mycket snabbare den SKU:n kan köra ett benchmark-standardtest. 

> [!IMPORTANT]
> ACU är endast en riktlinje.  Resultatet med din arbetsbelastning kan variera. 
> 
> 

<br>

| SKU-familj | ACU/kärna |
| --- | --- |
| [A0](../articles/virtual-machines/windows/sizes-general.md) |50 |
| [A1-A4](../articles/virtual-machines/windows/sizes-general.md) |100 |
| [A5-A7](../articles/virtual-machines/windows/sizes-general.md) |100 |
| [A1_v2-A8_v2](../articles/virtual-machines/windows/sizes-general.md) |100 |
| [A2m_v2-A8m_v2](../articles/virtual-machines/windows/sizes-general.md) |100 |
| [A8-A11](../articles/virtual-machines/windows/sizes-hpc.md) |225* |
| [D1-D14](../articles/virtual-machines/windows/sizes-general.md) |160 |
| [D1_v2-D15_v2](../articles/virtual-machines/windows/sizes-general.md) |210 - 250* |
| [DS1-DS14](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |160 |
| [DS1_v2-DS15_v2](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |210-250* |
| [F1-F16](../articles/virtual-machines/windows/sizes-compute.md) |210-250* |
| [F1s-F16s](../articles/virtual-machines/windows/sizes-compute.md) |210-250* |
| [G1-G5](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |180 - 240* |
| [GS1-GS5](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |180 - 240* |
| [H](../articles/virtual-machines/windows/sizes-hpc.md) |290 - 300* |
| [L4s-L32s](../articles/virtual-machines/windows/sizes-storage.md) |180 - 240* |

ACU:er som visas med * använder Intel® Turbo-teknik för att öka processorfrekvensen och prestanda.  Prestandaökningens storlek kan variera beroende på storleken på den virtuella datorn, arbetsbelastningen och andra arbetsbelastningar som körs på samma värd.
