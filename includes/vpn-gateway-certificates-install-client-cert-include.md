---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f860a0cebefaebe16c40abf008bd24e333eb80d5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
Om du vill skapa en P2S-anslutning från en annan klientdator än den som du använde för att generera klientcertifikat, måste du installera ett klientcertifikat. När du installerar ett klientcertifikat behöver du lösenordet som skapades när klientcertifikatet exporterades.

1. Leta upp och kopiera *.pfx*-filen till klientdatorn. Installera *.pfx*-filen genom att dubbelklicka på den på klientdatorn. Lämna **Lagringsplats** som **Aktuell användare** och klicka sedan på **Nästa**.
2. Gör inga ändringar på sidan för den **Fil** som ska importeras. Klicka på **Nästa**.
3. På den **skydd av privat nyckel** sidan, ange ett lösenord för certifikatet, eller kontrollera att säkerhetsobjektet är korrekt och klicka på **nästa**.
4. På sidan **Certifikatarkiv** låter du standardplatsen stå kvar och klickar sedan på **Nästa**.
5. Klicka på **Slutför**. På sidan **Säkerhetsvarning** för certifikatinstallationen klickar du på **Ja**. Eftersom du har genererat certifikatet kan du klicka på ”Ja” utan att oroa dig. Certifikatet har nu importerats.