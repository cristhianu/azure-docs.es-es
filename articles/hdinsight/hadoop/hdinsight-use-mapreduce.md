---
title: MapReduce con Apache Hadoop en HDInsight
description: Obtenga información sobre cómo ejecutar trabajos de Apache MapReduce en Apache Hadoop en clústeres de HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: c4f975b56d3658731b6dc165e01b54ac09f3b89c
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076230"
---
# <a name="use-mapreduce-in-apache-hadoop-on-hdinsight"></a>Uso de MapReduce con Apache Hadoop en HDInsight

Obtenga información sobre cómo ejecutar trabajos de MapReduce en clústeres de HDInsight.

## <a id="data"></a>Datos de ejemplo

HDInsight proporciona diversos conjuntos de datos de ejemplo que se almacenan en los directorios `/example/data` y `/HdiSamples`. Estos directorios están en el almacenamiento predeterminado de su clúster. En este documento, se utiliza el archivo `/example/data/gutenberg/davinci.txt`. Este archivo contiene los cuadernos de Leonardo Da Vinci.

## <a id="job"></a>MapReduce de ejemplo

En el clúster de HDInsight se incluye un MapReduce de ejemplo de aplicación de recuento de palabras. En este ejemplo se encuentra en `/example/jars/hadoop-mapreduce-examples.jar` en el almacenamiento predeterminado del clúster.

El código Java siguiente es el origen de la aplicación de MapReduce contenida en el archivo `hadoop-mapreduce-examples.jar`:

```java
package org.apache.hadoop.examples;

import java.io.IOException;
import java.util.StringTokenizer;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.util.GenericOptionsParser;

public class WordCount {

    public static class TokenizerMapper
        extends Mapper<Object, Text, Text, IntWritable>{

    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();

    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
        StringTokenizer itr = new StringTokenizer(value.toString());
        while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
        }
    }
    }

    public static class IntSumReducer
        extends Reducer<Text,IntWritable,Text,IntWritable> {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<IntWritable> values,
                        Context context
                        ) throws IOException, InterruptedException {
        int sum = 0;
        for (IntWritable val : values) {
        sum += val.get();
        }
        result.set(sum);
        context.write(key, result);
    }
    }

    public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
    if (otherArgs.length != 2) {
        System.err.println("Usage: wordcount <in> <out>");
        System.exit(2);
    }
    Job job = new Job(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
}
```

Para obtener instrucciones sobre cómo escribir sus propias aplicaciones de MapReduce, consulte el siguiente documento:

* [Desarrollo de aplicaciones MapReduce de Java para HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

## <a id="run"></a>Ejecución de MapReduce

HDInsight puede ejecutar trabajos de HiveQL mediante varios métodos. Use la tabla siguiente para decidir cuál es el método adecuado para usted luego siga el vínculo para ver un tutorial.

| **Use esto**... | **...para hacer esto** | ...con este **sistema operativo de clúster** | ...desde este **sistema operativo de cliente** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |Uso del comando Hadoop mediante **SSH** |Linux |Linux, Unix, Mac OS X o Windows |
| [Curl](apache-hadoop-use-mapreduce-curl.md) |Enviar el trabajo de forma remota mediante **REST** |Linux o Windows |Linux, Unix, Mac OS X o Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |Enviar el trabajo de forma remota mediante **Windows PowerShell** |Linux o Windows |Windows |

## <a id="nextsteps"></a>Pasos siguientes

Para conocer más acerca del trabajo con datos en HDInsight, consulte los siguientes documentos:

* [Desarrollo de programas MapReduce de Java para HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Uso de Apache Hive con HDInsight][hdinsight-use-hive]

* [Uso de Apache Pig con HDInsight][hdinsight-use-pig]

[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: apache-hadoop-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]:../hdinsight-use-hive.md
[hdinsight-use-pig]:hdinsight-use-pig.md


[powershell-install-configure]: /powershell/azureps-cmdlets-docs
