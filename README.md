# webAvanzada

### Respuestas del Laboratorio

**Pregunta 1:** ¿Por qué no se recomienda desarrollar directamente sobre main en este laboratorio?
Desarrollar directamente en `main` impide el uso de flujos de integración continua (CI) mediante Pull Requests. Utilizar una rama separada permite validar el código automáticamente antes de integrarlo, previniendo que errores lleguen a la rama principal.

**Pregunta 2:** ¿Qué problema se evita al utilizar --skip-git al crear el proyecto Angular?
Evita inicializar un sub-repositorio Git (una carpeta `.git` anidada) dentro de la carpeta `frontend/`. Esto causaría conflictos con el repositorio principal de Git y problemas en el seguimiento de los archivos del frontend.

**Pregunta 3:** ¿Qué verifica npm run build en esta etapa del laboratorio?
Verifica que el código fuente de Angular se compile correctamente (sin errores de sintaxis o configuración) de manera local antes de automatizar este mismo proceso en el entorno de Integración Continua (CI).

**Pregunta 4:** ¿Qué utilidad tiene revisar git status o git diff --cached antes de realizar un commit?
Sirve para confirmar exactamente qué archivos y cambios se han preparado para el commit, evitando versionar por accidente archivos temporales, dependencias (`node_modules`), secretos o cambios incompletos.

**Pregunta 5:** ¿Qué evento activa el workflow ci.yml?
Se activa mediante el evento `pull_request` dirigido a la rama `main`.

**Pregunta 6:** En runs-on: ubuntu-latest, ¿qué representa ubuntu-latest?
Representa el entorno de ejecución (runner) provisto por GitHub Actions, indicando que el pipeline se ejecutará en una máquina virtual con la versión más reciente de Ubuntu Linux.

**Pregunta 7:** Ordene las etapas de validación que ejecuta el job frontend y explique por qué npm ci se ejecuta antes que las pruebas.
El orden es:
1. Obtener código
2. Configurar Node.js
3. Instalar dependencias (`npm ci`)
4. Ejecutar pruebas (`npm test`)
5. Construir Angular (`npm run build`)
`npm ci` se ejecuta antes porque descarga e instala todas las dependencias exactas necesarias del proyecto; sin ellas, los comandos de prueba y construcción fallarían al no encontrar los paquetes requeridos.

**Pregunta 8:** Después del push, indique qué etapa del pipeline falla y qué ocurre con las etapas siguientes.
Falla la etapa "Ejecutar pruebas" (`npm test`). Al ocurrir este error, el pipeline se detiene inmediatamente, por lo que las etapas siguientes (como "Construir Angular") se omiten/cancelan.

**Pregunta 9:** ¿Debería integrarse este Pull Request a main mientras el pipeline está fallando? Justifique.
No, porque el propósito del CI es garantizar que solo código estable y verificado llegue a `main`. Integrarlo ignorando el fallo introduciría un código defectuoso en la rama principal.

**Pregunta 10:** Clasifique cada elemento como “versionable”, “variable/configuración” o “secreto/no versionable”:
- package.json: Versionable
- API_URL pública: Variable/configuración
- AWS_REGION: Variable/configuración
- DB_PASSWORD: Secreto/no versionable
- API_TOKEN: Secreto/no versionable
- terraform.tfstate: Secreto/no versionable

**Pregunta 11:** ¿Por qué una contraseña o token no debe escribirse directamente dentro de ci.yml, cd.yml o un archivo TypeScript del frontend?
Porque esos archivos quedan guardados en el historial de control de versiones. Cualquier persona o sistema con acceso al repositorio (presente o futuro) podría ver las credenciales en texto plano, comprometiendo la seguridad.

**Pregunta 12:** Si un secreto real fue incluido en un commit y luego se agrega su archivo a .gitignore, ¿queda solucionado el problema? Explique qué acción adicional debe realizarse.
No queda solucionado, ya que el archivo con el secreto permanece visible en el historial de commits anteriores de Git. Se debe revocar la credencial inmediatamente en el proveedor afectado, y adicionalmente reescribir el historial del repositorio para eliminar el rastro del secreto.

**Pregunta 13:** ¿Qué diferencia existe entre terraform validate, terraform plan y terraform apply?
- `terraform validate`: Verifica la sintaxis y validez semántica de la configuración local sin acceder a recursos reales.
- `terraform plan`: Evalúa la configuración frente al estado real y muestra un plan de ejecución con los cambios que se aplicarán (crear/modificar/eliminar).
- `terraform apply`: Ejecuta efectivamente esos cambios aplicándolos en la infraestructura.

**Pregunta 14:** ¿Por qué ci.yml se activa con pull_request y cd.yml se activa con push sobre main?
`ci.yml` busca validar el código propuesto de forma temprana *antes* de que sea integrado (durante la revisión del PR). `cd.yml` ejecuta el Despliegue Continuo de los cambios una vez que estos han sido aprobados y fusionados definitivamente (`push`) en `main`.

**Pregunta 15:** ¿Qué función cumple Terraform dentro de este flujo de CD?
Automatiza el aprovisionamiento y preparación del entorno (en este caso, un entorno de staging simulado), encargándose de copiar los archivos del frontend compilados hacia el directorio de destino.

**Pregunta 16:** ¿Por qué el workflow usa ${{ secrets.DEMO_TOKEN }} en lugar de escribir el valor directamente?
Para inyectar el valor del secreto de forma segura como variable de entorno durante la ejecución en el runner, manteniendo el valor real oculto en los logs públicos y completamente fuera del código fuente del repositorio.