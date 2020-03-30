---
title: Konfigurera en Azure Migrate-applianc
description: Lär dig hur du konfigurerar en Azure Migrera-installation för att bedöma och migrera virtuella virtuella datorer med VMware.
ms.topic: article
ms.date: 11/18/2019
ms.openlocfilehash: 0447443bb6a642cac09566450ea2d9bb6f6453d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337667"
---
# <a name="set-up-an-azure-migrate-appliance"></a>Konfigurera en Azure Migrate-apparat

I den här artikeln sammanfattas alternativ för att konfigurera Azure Migrate-installationen. 

Azure Migrate-enheten är en lokal distribution av lätta enheter och används i ett antal scenarier.

**Scenario** | **Detaljer**
--- | ---
Utvärdera virtuella virtuella datorer med Azure Migrate:Server Assessment | Identifiera VM-appar och vm-beroenden<br/><br/> Samla in datorns metadata och prestandametadata för utvärderingar.
Utvärdera virtuella virtuella hyper-v-datorer med Azure Migrate:Server Assessment | Upptäck virtuella virtuella hyper-virtuella datorer<br/><br/> Samla in datorns metadata och prestandametadata för utvärderingar.
Bedöma fysiska maskiner | Upptäck maskiner som fysiska servrar<br/><br/> Samla in datorns metadata och prestandametadata för utvärderingar.
Replikera virtuella virtuella datorer med agentlös migrering. | Replikera virtuella datorer med VMware utan att installera något på de virtuella datorer som du vill replikera.


## <a name="deployment-options"></a>Distributionsalternativ

Du kan distribuera apparaten på ett par olika sätt.

**Scenario** | **Mall** | **Skript** 
--- | --- | --- | ---
**Utvärdera virtuella datorer med VMware** | Distribuera med en nedladdad OVA-mall.<br/><br/> Lär dig hur du distribuerar enheten [med hjälp av en mall](how-to-set-up-appliance-vmware.md)eller startar [utvärderingshandledningen](tutorial-prepare-vmware.md) och distribuerar enheten med en mall under självstudien.  | Distribuera med ett PowerShell-installationsskript.<br/><br/>  [Granska](deploy-appliance-script.md) skriptinstruktioner för apparater.
**Bedöma virtuella virtuella hyper-virtuella datorer** | Distribuera med en nedladdad VHD-mall. <br/><br/> Lär dig hur du distribuerar enheten [med hjälp av en mall](how-to-set-up-appliance-vmware.md)eller startar [utvärderingshandledningen](tutorial-prepare-vmware.md) och distribuerar enheten med en mall under självstudien. | Distribuera med ett PowerShell-installationsskript.<br/><br/> [Granska](deploy-appliance-script.md) skriptinstruktioner för apparater. 
**Utvärdera fysiska servrar** | Ingen mall. | Distribuera med ett PowerShell-installationsskript.<br/><br/> Granska [skriptinstruktioner för apparatskript](how-to-set-up-appliance-physical.md)eller starta [utvärderingshandledningen](tutorial-prepare-physical.md)och distribuera apparaten under självstudien.
**Virtuella datorer med replikera vmware (agentless)** | Distribuera med en nedladdad OVA-mall.<br/><br/> Om du redan har bedömt virtuella datorer som du replikerar har du redan konfigurerat apparaten under bedömningen.<br/><br/> Om du replikerar virtuella datorer med VMware utan att bedöma dem kan du läsa om hur du distribuerar enheten med hjälp av en mall eller startar [den agentlösa migreringsdjälvstudien](tutorial-migrate-vmware.md)och distribuerar enheten med en mall under självstudien. | Distribuera med ett PowerShell-installationsskript. <br/><br/> [Granska](deploy-appliance-script.md) skriptinstruktioner för apparater. 




## <a name="next-steps"></a>Nästa steg

[Granska](migrate-appliance.md) apparatkraven.