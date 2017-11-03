---
title: Visa SAML som returneras av Access Control Service (Java)
description: "Lär dig hur du visar SAML som returneras av åtkomstkontrolltjänsten i Java-program i Azure."
services: active-directory
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 6cd216f9-eb43-46b4-b30d-f194d0ae2d48
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.custom: aaddev
ms.openlocfilehash: 1552e624a4703138ab82f7133ceaec3dbd04e1db
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-view-saml-returned-by-the-azure-access-control-service"></a>Så här visar du SAML som returnerats av tjänsten Azure Access Control
Den här guiden visar hur du visar den underliggande Security Assertion Markup Language (SAML) returneras till ditt program med Azure Access Control Service (ACS). Guiden bygger på den [så Web autentisera användare med Azure Access Control Service med Eclipse](active-directory-java-authenticate-users-access-control-eclipse.md) avsnittet genom att ange koden som visar SAML-information. Det färdiga programmet kommer att se ut ungefär så här.

![Exempel SAML-utdata][saml_output]

Mer information om ACS finns i [nästa steg](#next_steps) avsnitt.

> [!NOTE]
> Azure Access tjänstfilter kontrollen är en community technology preview. Som förhandsversionen stöds den formellt inte av Microsoft.
> 
> 

## <a name="prerequisites"></a>Krav
Om du vill utföra åtgärderna i den här guiden Slutför exemplet på [så Web autentisera användare med Azure Access Control Service med Eclipse](active-directory-java-authenticate-users-access-control-eclipse.md) och använda den som utgångspunkt för den här kursen.

## <a name="add-the-jspwriter-library-to-your-build-path-and-deployment-assembly"></a>Lägga till JspWriter bibliotek i sammansättningen build sökväg och distribution
Lägg till biblioteket som innehåller den **javax.servlet.jsp.JspWriter** klassen för att skapa sökvägen och distribution av sammansättningen. Om du använder Tomcat biblioteket är **jsp-api.jar**, som finns i Apache **lib** mapp.

1. I Eclipses Projektutforskaren, högerklickar du på **MyACSHelloWorld**, klickar du på **byggsökväg**, klickar du på **konfigurera byggsökväg**, klicka på den **bibliotek** fliken och klicka sedan på **lägga till externa JAR: er**.
2. I den **JAR markeringen** dialogrutan, navigera till den nödvändiga JAR markerar du den och klicka sedan på **öppna**.
3. Med den **egenskaper för MyACSHelloWorld** fortfarande öppna dialogrutan **distribution sammansättningen**.
4. I den **Web distribution sammansättningen** dialogrutan klickar du på **Lägg till**.
5. I den **nytt sammansättningen direktiv** dialogrutan klickar du på **Java Build Path poster** och klicka sedan på **nästa**.
6. Välj lämpligt bibliotek och klicka på **Slutför**.
7. Klicka på **OK** att stänga den **egenskaper för MyACSHelloWorld** dialogrutan.

## <a name="modify-the-jsp-file-to-display-saml"></a>Ändra JSP-fil om du vill visa SAML
Ändra **index.jsp** att använda följande kod.

    <%@ page language="java" contentType="text/html; charset=UTF-8"
        pageEncoding="UTF-8"%>
        <%@ page import="javax.xml.parsers.*"
                 import="javax.xml.transform.*"
                 import="org.w3c.dom.*"
                 import="java.io.*"
                 import="javax.xml.transform.stream.*"
                 import="javax.xml.transform.dom.*"
                 import="javax.xml.xpath.*"
                 import="javax.servlet.jsp.JspWriter" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Sample ACS Filter</title>
    </head>
    <body>
        <h3>SAML information from sample ACS program</h3>
        <%!
        void displaySAMLInfo(Node node, String parent, JspWriter out)
        {

            try
            {
                String nodeName;
                int nChild, i;

                nodeName = node.getNodeName();
                out.println("<br>");
                out.println("<u>Examining <b>" + parent + nodeName + "</b></u><br>");

                   // Attributes.
                   NamedNodeMap attribsMap = node.getAttributes();
                   if (null != attribsMap)
                   {
                         for (i=0; i < attribsMap.getLength(); i++)
                         {
                                Node attrib = attribsMap.item(i);
                                out.println("Attribute: <b>" + attrib.getNodeName() + "</b>: " + attrib.getNodeValue()  + "<br>");
                         }
                   }

                   // Child nodes.
                   NodeList list = node.getChildNodes();
                   if (null != list)
                    {
                          nChild = list.getLength();
                          if (nChild > 0)
                          {                    

                                 // If it is a text node, just print the text.
                                 if (list.item(0).getNodeName() == "#text")
                                 {
                                     out.println("Text value: <b>" + list.item(0).getTextContent() + "</b><br>");
                                 }
                                 else
                                 {
                                     // Print out the child node names.
                                     out.print("Contains " + nChild + " child node(s): ");   
                                        for (i=0; i < nChild; i++)
                                     {
                                        Node temp = list.item(i);

                                        out.print("<b>" + temp.getNodeName() + "</b>");
                                        if (i < nChild - 1)
                                        {
                                            // Separate the names.
                                            out.print(", ");
                                        }
                                        else
                                        {
                                            // Finish the sentence.
                                            out.print(".");
                                        }

                                     }
                                     out.println("<br>");

                                     // Process the child nodes.
                                     for (i=0; i < nChild; i++)
                                     {
                                        Node temp = list.item(i);
                                        displaySAMLInfo(temp, parent + nodeName + "\\", out);
                                     }
                               }
                          }
                      }
                  }
                catch (Exception e)
                {
                    System.out.println("Exception encountered.");
                    e.printStackTrace();            
                }
            }
        %>

        <%
        try
        {
            String data  = (String) request.getAttribute("ACSSAML");

            DocumentBuilder docBuilder;
            Document doc = null;
            DocumentBuilderFactory docBuilderFactory = DocumentBuilderFactory.newInstance();
            docBuilderFactory.setIgnoringElementContentWhitespace(true);
            docBuilder = docBuilderFactory.newDocumentBuilder();
            byte[] xmlDATA = data.getBytes();

            ByteArrayInputStream in = new ByteArrayInputStream(xmlDATA);
            doc = docBuilder.parse(in);
            doc.getDocumentElement().normalize();

            // Iterate the child nodes of the doc.
            NodeList list = doc.getChildNodes();

            for (int i=0; i < list.getLength(); i++)
            {
                displaySAMLInfo(list.item(i), "", out);
            }
        }
        catch (Exception e)
        {
            out.println("Exception encountered.");
            e.printStackTrace();
        }

        %>
    </body>
    </html>

## <a name="run-the-application"></a>Köra programmet
1. Kör ditt program i emulatorn dator eller distribuera till Azure med hjälp av stegen som beskrivs i [så Web autentisera användare med Azure Access Control Service med Eclipse](active-directory-java-authenticate-users-access-control-eclipse.md).
2. Starta en webbläsare och öppna ditt webbprogram. När du loggar in på ditt program, ser du SAML-information, inklusive den security assertion tillhandahållits av identitetsleverantören.

## <a name="next-steps"></a>Nästa steg
Utforska att ytterligare funktioner för Gransknings-och insamlingstjänstens och om du vill experimentera med mer avancerade scenarier, se [Access Control Service 2.0][Access Control Service 2.0].

[Prerequisites]: #pre
[Modify the JSP file to display SAML]: #modify_jsp
[Add the JspWriter library to your build path and deployment assembly]: #add_library
[Run the application]: #run_application
[Next steps]: #next_steps
[Access Control Service 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[How to Authenticate Web Users with Azure Access Control Service Using Eclipse]: active-directory-java-authenticate-users-access-control-eclipse
[saml_output]: ./media/active-directory-java-view-saml-returned-by-access-control/SAML_Output.png
