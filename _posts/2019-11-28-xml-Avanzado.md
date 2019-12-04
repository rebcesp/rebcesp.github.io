---
layout: post
title: Tratando con el DOM de un XML Avanzado
category: XML
permalink: "blog/xml-Avanzado"
published: yes
---

<img class="differentSize40" src="https://lisia2.files.wordpress.com/2015/02/xml_logo.gif" alt="logoXML" style="margin:auto; display:block;">

# _0x01 - Introdución_

Una de las cosas interesantes que necesitaba hacer en este gran XML como yo lo llamo `XML - Avanzado` por el hecho que se trata de un XML compuesto que contiene más XML valga la redudancia y scripts en `Javascript` suele ser complejo al parsearlo y trabajar con los Nodos que necesito.

Entonces nos pusimos a diseccionarlo y trabajar con el XML que realmente necesitabamos , el cual era el `Xml-Template`


# _0x02 - Diseccionando_

Una de las cosas mas difícil en estas ocasiones es diseccionarlo y trabajar justamente con lo que necesito , con las etiquetas de `Tipo Complejo` ya que este XML tiene 2887 líneas !!!

<img class="differenteSize65" src="/assets/img/xmlEtiquetas.png" alt="LineasXml" style="margin:auto; display:block;">

Necesito trabajar con un nodo de etiquetas , específicamente la etiqueta `</draw>` donde aquí dentro integraremos las otras etiquetas de `tipo complejo` , esto va a ser necesario para que cuando el formulario el cual estoy trabajando se visualize en un PDF , cabe decir que son formularios con accesibilidad para personas con discapacidades , volviendo al tema cuando se visualize el formulario al apretar la tecla `TAB` el **setFocus** que es el método que esta en el Backend del formulario programado en Javascript , debería enfocar los campos secuencialmente y gracias a la herramienta [NVDA](https://nvda.es/) qué es un lector de pantalla la persona con la discapacidad de visión debería escuchar los nombres de los campos. 

# _0x03 - Picando Código (Java)_

Ya que sabemos con los nodos que trabajaremos, empezaremos a programar en Java para tratarlos y poder hacer nuestra tarea, Java es un lenguaje muy robusto, tiene muchas librerías que nos permitirá profundizar en nuestro fichero.

## Este código ire explicando paso a paso que es lo que he hecho.

```java
import java.io.IOException;
import java.util.ArrayList;
import java.util.Collection;
import javax.xml.parsers.DocumentBuilder;
import javax.xml.parsers.DocumentBuilderFactory;
import javax.xml.parsers.ParserConfigurationException;
import javax.xml.transform.Transformer;
import javax.xml.transform.TransformerException;
import javax.xml.transform.TransformerFactory;
import javax.xml.transform.dom.DOMSource;
import javax.xml.transform.stream.StreamResult;

import org.w3c.dom.Document;
import org.w3c.dom.Element;
import org.w3c.dom.Node;
import org.w3c.dom.NodeList;
import org.xml.sax.SAXException;

public class prueba {

	public static void main(String[] args) throws TransformerException, ParserConfigurationException, SAXException, IOException {
		
		
	  DocumentBuilderFactory documentBuilderFactory = DocumentBuilderFactory.newInstance();
		DocumentBuilder documentBuilder = documentBuilderFactory.newDocumentBuilder();
		Document document = documentBuilder.parse("C:\\Users\\UsuarioRebcesp\\Desktop\\prueba.xml");
		Element root = document.getDocumentElement();
		Element rootElement = document.getDocumentElement();
			    
		//Tomamos el elemento con el que nos interesa trabajar
		NodeList nList = document.getElementsByTagName("draw");
		System.out.println("Numero de extras: " + nList.getLength()); //longitud de las etiquetas = 3
			    
			
			    
			    
			    
			    for (int temp = 0; temp < nList.getLength(); temp++) {
			    	
			        Collection<Prueba> svr = new ArrayList<Prueba>();
				    svr.add(new Prueba());
			    	
			    	
			    	for (Prueba i : svr){
			    		Element dibujar = document.createElement("extras");
			    		
			    		Element text = document.createElement("text");
			    		text.setAttribute("name", "Guillermo");
			    		dibujar.appendChild(text);
			    		
			    	    
			    	}
			    	
			    	   Node nNode = nList.item(temp);
			    	   if(nNode.getNodeType() == Node.ELEMENT_NODE){
			            	Element eElement = (Element) nNode;
			            	
			            	if(eElement.hasChildNodes()){
			            			NodeList nl = nNode.getChildNodes();
			            			for(int j=0; j<nl.getLength(); j++){
			            				Node nd = nl.item(j);
			            				System.out.println(nd.getTextContent());
			            			}
			            			
			            	
			            	

		DOMSource source = new DOMSource(document);
    TransformerFactory transformerFactory = TransformerFactory.newInstance();
		Transformer transformer = transformerFactory.newTransformer();
		StreamResult result = new StreamResult("C:\\Users\\UsuarioRebcesp\\Desktop\\prueba.xml");
		transformer.transform(source, result);
			    	
			    	
						    
					    	  
			    	
       }
		  }
     }
    }
  }
```



