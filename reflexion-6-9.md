# Reflexión 6.9

1. La principal diferencia entre `git reset` y `git revert` es cómo manejan el historial. `git reset` mueve el puntero de la rama hacia atrás en el tiempo, reescribiendo la historia y perdiendo los commits posteriores en esa rama. Por otro lado, `git revert` no borra nada; en su lugar, crea un commit completamente nuevo que invierte los cambios introducidos por un commit anterior, manteniendo intacto el historial original.
2. Preferiría usar `git revert` en las siguientes situaciones:
   - Cuando necesito deshacer un cambio en la rama `main` que ya ha sido pusheado y compartido con el resto del equipo, para evitar problemas de divergencia.
   - Cuando quiero deshacer una funcionalidad que causó un bug, pero deseo dejar un registro claro en el historial de que el código original existió y fue deshecho intencionalmente.
3. Se dice que `git revert` es un "forward-moving undo" porque deshace cambios avanzando en el historial (creando nuevos commits) en lugar de retroceder (borrando commits). Esto es crucial en un proyecto colaborativo porque evita que el historial cambie para otros desarrolladores que ya han hecho pull, lo cual causaría errores severos al intentar sincronizar sus repositorios locales.
