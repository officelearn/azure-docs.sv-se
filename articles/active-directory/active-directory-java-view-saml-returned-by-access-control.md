<properties
    pageTitle="Anzeigen der vom Azure Access Control Service zurückgegebenen SAML (Java)"
    description="Erfahren Sie, wie Sie die vom Access Control Service in auf Azure gehosteten Java-Anwendungen zurückgegebene SAML anzeigen."
    services="active-directory" 
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor="jimbe" />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/12/2015" 
    ms.author="robmcm" />

# Anzeigen der vom Azure Access Control Service zurückgegebenen SAML

Diese Anleitung erklärt, wie Sie die Security Assertion Markup Language (SAML) anzeigen können, die vom Azure Access Control Service (ACS) an Ihre Anwendung zurückgegeben wurde. Das Handbuch baut auf den [Authentifizieren von Webbenutzern mit Azure Access Control Service über Eclipse][] Thema, durch die Bereitstellung von Code, mit die SAML-Informationen angezeigt. Die fertige Anwendung sieht etwa wie folgt aus.

![Beispiel-SAML-Ausgabe][saml_output]

Weitere Informationen zum ACS finden Sie unter der [Nächste Schritte](#next_steps) Abschnitt.

> [AZURE.NOTE]
> Der Azure Access Control Service-Filter ist eine Technologievorschau für die Community. Als Vorabversion wird diese Software von Microsoft nicht offiziell unterstützt.

## Voraussetzungen

Zum Ausführen der Aufgaben in dieser Anleitung führen Sie das Beispiel unter [Authentifizieren von Webbenutzern mit Azure Access Control Service über Eclipse][] und als Ausgangspunkt für dieses Lernprogramm verwenden.

## Fügen Sie die JspWriter-Bibliothek zu Ihrem Buildpfad und zu Ihrer Bereitstellungs-Assembly hinzu

Fügen Sie die Bibliothek, enthält die **javax.servlet.jsp.JspWriter** Klasse, um Ihre Build und zur Bereitstellungsassembly. Falls Sie Tomcat verwenden, ist **Jsp-api.jar**, befindet sich im Apache **Lib** Ordner.

1. Eclipse-Projektexplorer mit der Maustaste **MyACSHelloWorld**, klicken Sie auf **Buildpfad**, klicken Sie auf **Buildpfad konfigurieren**, klicken Sie auf die **Bibliotheken** Registerkarte, und klicken Sie dann auf **Add External JARs**.
2. In der **JAR Selection** Dialogfeld, navigieren Sie zum entsprechenden JAR, wählen Sie sie und klicken Sie dann auf **Öffnen**.
3. Mit der **Properties for MyACSHelloWorld** immer noch geöffneten Dialogfeld auf **Deployment Assembly**.
4. In der **Web Deployment Assembly** Dialogfeld klicken Sie auf **Hinzufügen**.
5. In der **New Assembly Directive** Dialogfeld klicken Sie auf **Java Build Path Entries** und klicken Sie dann auf **Weiter**.
6. Wählen Sie die entsprechende Bibliothek, und klicken Sie auf **Fertig stellen**.
7. Klicken Sie auf **OK** Schließen die **Properties for MyACSHelloWorld** Dialogfeld.

## Ändern der JSP-Datei zur Anzeige von SAML

Ändern Sie **index.jsp** den folgenden Code verwenden.

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

## Ausführen der Anwendung

1. Führen Sie die Anwendung im Serveremulator oder als Bereitstellung in Azure mithilfe der Schritte unter [Authentifizieren von Webbenutzern mit Azure Access Control Service über Eclipse][].
2. Öffnen Sie Ihre Webanwendung in einem Browser. Nach der Anmeldung sehen Sie in Ihrer Anwendung die SAML-Informationen inklusive der Security Assertion des Identitätsanbieters.

## Nächste Schritte

Die ACS-Funktionalität genauer erforschen und mit anspruchsvolleren Szenarien experimentieren möchten, finden Sie unter [Access Control Service 2.0][].

[Prerequisites]: #pre
[Modify the JSP file to display SAML]: #modify_jsp
[Add the JspWriter library to your build path and deployment assembly]: #add_library
[Run the application]: #run_application
[Next steps]: #next_steps
[Access Control Service 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[How to Authenticate Web Users with Azure Access Control Service Using Eclipse]: ../active-directory-java-authenticate-users-access-control-eclipse
[saml_output]: ./media/active-directory-java-view-saml-returned-by-access-control/SAML_Output.png
 

