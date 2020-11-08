---
title: 'Självstudie: kom igång med Azure Synapse Analytics – övervaka din Synapse-arbetsyta'
description: I den här självstudien får du lära dig hur du övervakar aktiviteter i din Synapse-arbetsyta.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: monitoring
ms.topic: tutorial
ms.date: 10/15/2020
ms.openlocfilehash: 924ac3a58a005b84cbf87f833e97862e26a5b8e0
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94363365"
---
# <a name="monitor-your-synapse-workspace"></a>Övervaka din Synapse-arbetsyta

I den här självstudien får du lära dig hur du övervakar aktiviteter i din Synapse-arbetsyta. Du kan övervaka aktuella och historiska aktiviteter för SQL, Apache Spark och pipeliner. 

## <a name="introduction-to-the-monitor-hub"></a>Introduktion till Monitor Hub

Öppna Synapse Studio och navigera till **Monitor** Hub. Här kan du se en historik över alla aktiviteter som sker i arbets ytan och vilka som är aktiva nu. 

* Under **integrering** kan du övervaka pipelines, utlösare och integrerings körningar
* Under **aktiviteter** kan du övervaka Spark-och SQL-aktiviteter. 

## <a name="integration"></a>Integrering

1. Gå till **pipeline för Integration >**. I den här vyn kan du se varje gång en pipeline har körts i din arbets yta. 
1. Hitta den pipeline som du körde i föregående steg och klicka på dess **pipeliniska namn**.
1. Nu kan du se hur enskilda aktiviteter i den pipelinen körs.
1. Klicka på **navigerings fältet** längst upp i Synapse Studio, klicka på **alla pipelines körs** för att återgå till den föregående vyn.

## <a name="apache-spark-activities"></a>Apache Spark aktiviteter

1. Gå till **integrerings > aktiviteter > Apache Spark program**. Nu kan du se alla Spark-program som körs eller har körts i din arbets yta.
1. Hitta ett program som inte längre körs och klicka på dess **program namn**. Nu kan du se information om Spark-programmet.
1. Om du är bekant med Apache Spark kan du hitta standard gränssnittet för Apache Spark historik Server genom att klicka på **Spark historik Server**.

## <a name="sql-activities"></a>SQL-aktiviteter

1. Gå till **integrerings > aktiviteter > SQL-begäranden**.
1. I den här vyn kan du se SQL-begäranden.
1. Välj en **pool** som ska övervakas. Nu kan du se alla SQL-begäranden som körs eller har körts i din arbets yta i den poolen.
1. Hitta en speciell SQL-begäran och hovra musen över det objektet. När du har hovrat ser du att en SQL-skript ikon visas.
1. Klicka på SQL-skriptfilen för att se all text i SQL-begäran.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska kunskaps centret](get-started-knowledge-center.md)
