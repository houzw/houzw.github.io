使用jena开发本体应用程序时，首先需要对我们利用本体构建工具，如protege等，构建的本体文件，如owl、rdf等读取并解析得到本体模型。下面给出相应的代码，不对的地方请指正。

（基于jena 2.13.0，jena 3 需要java 1.8）

方式一：使用RDFDataMgr

```java
    /**
     * 使用RDFDataMgr读取RDF文件
     *
     * @param fileName URI或 file:filename形式路径
     * @return
     */
    public static OntModel openRDFFile(String fileName)
    {
        OntModel model = ModelFactory.createOntologyModel(OntModelSpec.OWL_MEM);
        RDFDataMgr.read(model, fileName);
		return model;
    }
```
方式二：使用FileManager

```java
    /**
     * 打开并读取RDF文件
     *
     * @param fileName
     * @return
     */public static OntModel openRdfFile(String fileName)
    {
        OntModel model = ModelFactory.createOntologyModel(OntModelSpec.OWL_MEM);
        InputStream in = FileManager.get().open(fileName);
        OntModel baseOntModel = null;
		if (in != null)
        {
            baseOntModel = (OntModel) model.read(in, "");
        }
		return baseOntModel;
    }
```
上面两种方式都没有处理本体导入（import）问题，也即不能同时读取你在构建本体时引入的其他本体文件。为了处理引用的本体，本人目前发现最好的方式是使用配置文件：`location-mapping.ttl`

```yaml
@prefix rdf:        <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix rdfs:        <http://www.w3.org/2000/01/rdf-schema#> .
@prefix xsd:        <http://www.w3.org/2001/XMLSchema#> .
@prefix lm:         <http://jena.hpl.hp.com/2004/08/location-mapping#> .

# 地址映射
# Application location to alternative location mappings.
# + name 指本体本体文件的uri。可以是http开头的url，也可以是file:开头的文件地址。引入文件中的name一般是uri
# + altName 指文件实际的地址，可以是file:开头的本地文件，或网络地址
# + 顺序无关.
#
# The translation algorithm is:#
# 1 - Exact mappings: these are tried before attempting a prefix match.
# 2 - By prefix: find the longest matching prefix
# 3 - Use the original if no alternative.

[] lm:mapping
   [
     lm:prefix "GTS" ;
     lm:altPrefix "http://www.geodataont.cn/support/geology/gts"
   ],
   [
     lm:name "http://www.geodataont.cn/support/geology/gts";     
	 lm:altName "file:*/GTS.owl" # 请将*替换为文件目录
   ]
```
prefix部分补充：当利用 lm:altName 找不到对应文件时，会查找与lm:name匹配的最长的lm:prefix，利用其lm:altPrefix和lm:altName组合到一起最为地址去查找文件。

若有：

```yaml
lm:name "http://www.geodataont.cn/support/geology/gts/a.owl" ;
lm:altName "file:*/a.owl"
```
则可以配置为：

```yaml
lm:prefix "http://www.geodataont.cn/support/geology/gts" ;
lm:altPrefix "file:*/"
lm:name "http://www.geodataont.cn/support/geology/gts/a.owl" ;
lm:altName "a.owl"
```
（2016-04-09）

相应的代码如下

```java
    /**
     * 读取本体，使用location-mapper确定引用文件地址
     *
     * @param locMapperPath
     * @param baseOntPath 读取的本体文件路径
     * @return
     */
    public static OntModel loadOntModelWithLocMapper(String locMapperPath, String baseOntPath)
    {
        OntModel model = ModelFactory.createOntologyModel();

        LocationMapper locMapper = readLocationMapper(locMapperPath);

        model.getDocumentManager().setProcessImports(true);
        FileManager fileManager = model.getDocumentManager().getFileManager();
        fileManager.setLocationMapper(locMapper);
        baseOntPath = StringUtil.getUriFilePath(baseOntPath);// 添加file:，替换\        fileManager.readModel(model, baseOntPath);
        model.loadImports();
		return model;
    }
```

其中getUriFilePath是将文件地址进行转换，方便读取

```java

public static String getUriFilePath(String filepath)
{        
	if (filepath.startsWith("file:") || filepath.startsWith("http://"))         
		 return filepath;     
	else
    {
        filepath = "file:" + filepath;
        filepath = filepath.replaceAll("\\\\", "/");           
		return filepath;
    }
}
```

目前基本功能就是这样，详细内容参考jena的文档。不对的地方一起讨论
