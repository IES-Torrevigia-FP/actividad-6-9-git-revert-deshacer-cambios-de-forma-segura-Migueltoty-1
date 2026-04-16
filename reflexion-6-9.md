La diferencia fundamental es que git reset modifica el historial eliminando commits pasados, mientras que git revert preserva el historial creando un nuevo commit que deshace los cambios de otro commit anterior





Prefiero usar git revert en lugar de git reset para deshacer cambios que ya han sido subidos a un repositorio remoto compartido, ya que revert crea un nuevo commit de reversión sin alterar el historial existente, evitando conflictos para otros desarrolladores





git revert es considerado un "deshacer avanzando" (forward-moving undo) porque no borra ni modifica el historial de commits existente, sino que crea un nuevo commit que aplica los cambios inversos para anular los efectos de uno anterior
