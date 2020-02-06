---
layout: post
title: Accesibilidad Forms(Discapacidad Visual) / Visual Disability - Forms Accesibility
category: XML
permalink: "blog/xml-Avanzado"
published: yes
---

<img class="differentSize40" src="https://lisia2.files.wordpress.com/2015/02/xml_logo.gif" alt="logoXML" style="margin:auto; display:block;">

# _0x01 - Introdución_

Este es un proyecto el cual fue muy ambicioso y me genero muchas ganas poder conseguirlo y terminarlo ya que estaría ayudando a muchas personas con discapacidad visual, se trata de un proyecto que trata los archivos PDF para mejorar la accesibilidad en personas con discapacidad visual valga la redudancia y hacer mas fácil su tarea a la hora de leer formularios y rellenarlos.


# _0x02 - Diseccionando_

El objetivo del proyecto era primeramente diseccionar un archivo `XML Compuesto` como he mencionado anteriormente y tratar con los nodos principales, nodos hijos, elementos y atributos, en esta ocasión de la foto necesitaba agregar este bloque de nodos para añadir los `marcadores/etiquetas` la cual sería la funcionalidad accesible para los `"discapacitados"`.

<img class="differenteSize65" src="/assets/img/xmlEtiquetas.png" alt="LineasXml" style="margin:auto; display:block;">

Voy a explicar el bloque del nodo de la fotografía.

---
Extras = Es la etiqueta necesaria que se necesita para crear un marcador en un contenedor válido.
Text "name" =  Es el nombre del marcador que aparecerá en el panel de marcadores. Si no se especifica, el marcador no se generará
Text "color" = El color es el que se representará el nombre del marcador. El parámetro de color de indicarse en el esquema RGB. Por ejemplo, para insertar un marcador en color rojo, este parámetro debe especificarse como `255,0,0`. El valor predeterminado para el parámetro de color es `0,0,0,(negro)`.
Text "style" = El estilo en el que se representa el nombre del marcador.
Text "action" = La acción que se realiza cuando se hace clic en el marcador. Los valores pueden ser:
goToPage / setFocus / Execute/
El que yo utilizo es setFocus que establece en el campo principal en foco, es decir al seleccionar el marcador tabulando se dirige directamente al campo
---


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



