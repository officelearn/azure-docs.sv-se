---
title: 'Arbeta via fjärr anslutning med skydds: Azure skydds'
description: På den här sidan beskrivs hur du kan använda Azure-skydds för att aktivera fjärrhantering på grund av COVID-19-Pandemic.
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mialdrid
ms.openlocfilehash: 182195190fed70b46185f98f595de6b6c32bbffe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80619386"
---
# <a name="working-remotely-using-azure-bastion"></a>Arbeta via fjärr anslutning med Azure skydds

Azure skydds spelar en pivot-roll i stödjande scenarier för fjärran vändare genom att tillåta användare med Internet anslutning att komma åt Azure Virtual Machines. Det gör det möjligt för IT-administratörer att hantera sina program som körs i Azure när som helst och var som helst runtom i hela världen.

>[!NOTE]
>Den här artikeln beskriver hur du kan använda Azure skydds, Azure, Microsoft Network och Azure-partnerns eko system för att arbeta fjärran slutet och minimera nätverks problem som du står inför på grund av COVID-19-kris.
>

## <a name="securely-access-virtual-machines"></a>Få säker åtkomst till virtuella datorer

Mer specifikt ger Azure skydds säker och sömlös RDP/SSH-anslutning till virtuella datorer i det virtuella Azure-nätverket, direkt i Azure Portal utan att använda en offentlig IP-adress. Mer information om Azure skydds-arkitekturen och viktiga funktioner finns i [Vad är Azure skydds](bastion-overview.md).

Azure-skydds distribueras per virtuellt nätverk, vilket innebär att företag kan konfigurera och hantera en Azure-skydds för att snabbt stödja fjärran vändare till virtuella datorer i ett virtuellt Azure-nätverk. Anvisningar om hur du skapar och hanterar Azure-skydds finns i [skapa en skydds-värd](bastion-create-host-portal.md).

## <a name="next-steps"></a>Nästa steg

* Konfigurera Azure-skydds med hjälp av [Azure Portal](bastion-create-host-portal.md), [POWERSHELL](bastion-create-host-powershell.md)eller Azure CLI.

* Mer information finns i [vanliga frågor och svar om skydds](bastion-faq.md) .
