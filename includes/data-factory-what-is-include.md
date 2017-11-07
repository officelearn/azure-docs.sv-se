
<!--
    As of 2017/10/06, this 'include' file is meant to replace the first paragraph of plain text that is duplicated at the top inside every tutorial-*.md article in azure-docs-pr/articles/data-factory/.

    This 'include' file is basically one paragraph.
    It explains what Azure Data Factory is, to someone who knows nothing about ADF.
-->

Azure Data Factory är en tjänst för integrering av data. Den låter dig skapa datadrivna arbetsflöden i molnet. Ett arbetsflöde implementeras som en eller flera *pipelines*. Pipelines organiserar och automatiserar flytt och transformering av data. Pipelines kan utföra följande sekvens med dina data:

1. Mata in data från olika datalager.
2. Omvandla eller bearbeta data med hjälp av beräkningstjänster, till exempel följande:
    - Azure HDInsight Hadoop
    - Spark
    - Azure Data Lake Analytics
    - Azure Machine Learning
3. Publicera utdata till datalager.
    - Publiceringsmålet kan vara en Azure SQL Data Warehouse som sedan kan användas av Business Intelligence-program (BI). 

Du kan schemalägga pipelines med hjälp av Data Factory.

