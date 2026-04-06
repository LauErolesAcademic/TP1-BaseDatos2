
<!-- revisión profe -->

[!DB-Mascotas]</br>
**Ámbito:** El seguimiento de mascotas que se dan en adopción</br>
**Problema a resolver:** Necesitan registrar qué mascotas hay, quién las adopta, cómo evolucionan post-adopción, y su historial médico/vacunas

**H1:** Una mascota pertenece a un único refugio u organización en un momento dado</br>
**H2:** Un adoptante puede adoptar más de una mascota, pero no al mismo tiempo (no solapadas) </br>
**H3:** Una mascota solo puede tener un proceso de adopción activo a la vez</br>
**H4:** El seguimiento post-adopción se registra con visitas/reportes periódicos</br>
**H5:** Una vacuna tiene fecha de aplicación y fecha de vencimiento</br>
**H6:** Un veterinario puede atender mascotas de distintos refugios</br>
**H7:** El estado de una mascota tiene dominio de valores: disponible, en_proceso, adoptada, en_tránsito</br>
**H8:** Un voluntario/foster puede tener una mascota temporalmente antes de la adopción definitiva</br>
**H9:** No se puede registrar una adopción si la mascota no está en estado disponible</br>
**H10:** El historial médico pertenece a la mascota, no al adoptante</br>


**ENTIDADES MAESTRO**
<br>

**Mascota**
<li>id_mascota (PK)</li>
<li>nombre</li>
<li>especie → [gato, perro, otro]</li>
<li>raza</li>
<li>fecha_nacimiento</li>
<li>sexo</li>
<li>estado → [disponible, en_proceso, adoptada, en_tránsito]</li>
<li>descripcion</li>
<li>id_refugio (FK)</li>
<br>

**Adoptante**

<li>id_adoptante(PK)</li>
<li>dni</li>
<li>nombre_completo</li>
<li>telefono</li>
<li>email</li>
<li>domicilio</li>
<li>fecha_registro</li>
<br>

**Refugio**

<li>id_refugio (PK)</li>
<li>nombre</li>
<li>domicilio</li>
<li>telefono</li>
<li>email</li>
<li>responsable</li>
<br>

**Veterinario**

<li>id_veterinario (PK)</li>
<li>nombre_completo</li>
<li>matricula</li>
<li>telefono</li>
<li>especialidad</li>
<br>

**Voluntario transito**

<li>id_voluntario (PK)</li>
<li>nombre_completo</li>
<li>dni</li>
<li>telefono</li>
<li>email</li>


**ENTIDADES TRANSACCIONALES**
<br>

**Adopcion**

<li>id_adopcion (PK)</li>
<li>id_mascota (FK)</li>
<li>id_adoptante (FK)</li>
<li>fecha_solicitud</li>
<li>fecha_adopcion</li>
<li>estado_proceso → [pendiente, aprobada, rechazada, completada]</li>
<br>

**Visita_Seguimiento(post-adopción)**

<li>id_visita (PK)</li>
<li>id_adopcion (FK)</li>
<li>fecha_visita</li>
<li>observaciones</li>
<li>estado_mascota_reportado → [excelente, bien, regular, mal]</li>
<li>id_voluntario (FK) (quién hizo el seguimiento)</li>

<br>

**Atencion_Medica**

<li>id_atencion (PK)</li>
<li>id_mascota (FK)</li>
<li>id_veterinario (FK)</li>
<li>fecha_atencion</li>
<li>motivo</li>
<li>diagnostico</li>
<li>tratamiento</li>
<br>

**Vacuna**

<li>id_vacuna (PK)</li>
<li>id_mascota (FK)</li>
<li>id_veterinario (FK)</li>
<li>nombre_vacuna</li>
<li>fecha_aplicacion</li>
<li>fecha_vencimiento</li>
<li>lote</li>
<br>

**transito_Temporal (mascota en tránsito con voluntario)**

<li>id_foster (PK)</li>
<li>id_mascota (FK)</li>
<li>id_voluntario (FK)</li>
<li>fecha_inicio</li>
<li>fecha_fin</li>
<li>observaciones</li>
<br>

**ACCIONES (Pseudocodigo store proceduer)**

check_disponible(id_mascota) → TRUE/FALSE<br>
  TRUE si: mascota.estado = 'disponible'<br>
  FALSE: "mascota no disponible para adopción"<br>

check_vacuna_vigente(id_mascota, nombre_vacuna) → TRUE/FALSE<br>
  TRUE si: SYSDATE <= vacuna.fecha_vencimiento<br>
  FALSE: "vacuna vencida"<br>

registrar_adopcion(id_mascota, id_adoptante) → OK/ERROR<br>
  Validar check_disponible()<br>
  Insertar en Adopcion<br>
  Actualizar mascota.estado = 'en_proceso'<br>

registrar_seguimiento(id_adopcion, observaciones, estado)<br>
  Validar que la adopcion.estado = 'completada'<br>
  Insertar en Visita_Seguimiento<br>