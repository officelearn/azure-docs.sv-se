---
title: 'Självstudie: kom igång med Azure Synapse Analytics – övervaka din Synapse-arbetsyta'
description: I den här självstudien får du lära dig hur du övervakar aktiviteter i din Synapse-arbetsyta.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/27/2020
ms.openlocfilehash: f8be96fb008471d040839141a230c13b8f1657a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708180"
---
# <a name="monitor-your-synapse-workspace"></a>Övervaka din Synapse-arbetsyta

I den här självstudien får du lära dig hur du övervakar aktiviteter i din Synapse-arbetsyta. Du kan övervaka aktuella och historiska aktiviteter för SQL Apache Spark. och pipeliner. 

## <a name="introduction-to-the-monitor-hub"></a>Introduktion till Monitor Hub

Öppna Synapse Studio och navigera till **Monitor** Hub. Här kan du se en historik över alla aktiviteter som sker i arbets ytan och vilka som är aktiva nu. 

* Under **dirigering**kan du övervaka pipelines, utlösare och integrerings körningar
* Under **aktiviteter**kan du övervaka Spark-och SQL-aktiviteter. 

## <a name="orchestration"></a>Orkestrering

1. Gå till **orchestration > pipelinen**. I den här vyn kan du se varje gång en pipeline har körts i din arbets yta. 
1. Hitta den pipeline som du körde i föregående steg och klicka på dess **pipeliniska namn**.
1. Nu kan du se hur enskilda aktiviteter i den pipelinen körs.
1. Klicka på **navigerings fältet** längst upp i Synapse Studio, klicka på **alla pipelines körs** för att återgå till den föregående vyn.

## <a name="apache-spark-activities"></a>Apache Spark aktiviteter

1. Gå till **orchestration > aktiviteter > Apache Spark program**. Nu kan du se alla Spark-program som körs eller har körts i din arbets yta.
1. Hitta ett program som inte längre körs och klicka på dess **program namn**. Nu kan du se information om Spark-programmet.
1. Om du är bekant med Apache Spark kan du hitta standard gränssnittet för Apache Spark historik Server genom att klicka på **Spark historik Server**.

## <a name="sql-activities"></a>SQL-aktiviteter

1. Gå till **orchestration > aktiviteter > SQL-begäranden**.
1. I den här vyn kan du se SQL-begäranden.
1. Välj en **pool** som ska övervakas. Nu kan du se alla SQL-begäranden som körs eller har körts i din arbets yta i den poolen.
1. Hitta en speciell SQL-begäran och hovra musen över det objektet. När du har hovrat ser du att en SQL-skript ikon visas.
1. Klicka på SQL-skriptfilen för att se all text i SQL-begäran.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska kunskaps centret](get-started-knowledge-center.md)
