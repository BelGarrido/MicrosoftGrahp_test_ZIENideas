URL: https://cut-curse-50956828.figma.site/


# Entrega de Prueba Técnica: Irishawk Cybersecurity

**Prueba B · Panel de ciberseguridad de un sistema**
**Reto:** Mostrar lo crítico de un sistema

---

## 1. Visión estratégica del diseño

> "Mi enfoque en este reto se ha centrado en resolver la **fatiga por alertas** y la **sobrecarga cognitiva** que sufren los operadores de un SOC (Security Operations Center).
> En entornos críticos, más información no significa mejor seguridad: el ruido operativo diluye lo verdaderamente importante. Por eso he diseñado el panel pensando en una persona concreta, el analista, y en una pregunta concreta: **¿qué miro primero y qué hago?**"

La prueba valora qué se decide mostrar y por qué, no lo vistoso que quede. Cada elemento de la pantalla responde a una regla explícita que se puede explicar y probar.

---

## 2. Alcance y reglas respetadas

| Regla de la prueba | Cómo la cumple el panel |
|---|---|
| Solo lo asignado | Se analiza únicamente el sistema entregado, nunca la red corporativa ni equipos de terceros |
| Nada ofensivo | Sin malware, exploits ni herramientas de ataque, ni como demostración |
| Datos protegidos | Los datos sensibles (usuarios, IPs, nombres de equipo) se anonimizan en el panel |
| Solo lectura | El panel observa y no modifica el sistema ni pide permisos de más |
| Datos reales | Nada inventado: al menos 2 fuentes distintas del sistema asignado |

---

## 3. Pilares de la solución

### Pilar 1. Jerarquía visual estricta

El color se reserva para el estado, nunca para decorar. Solo existen tres: correcto, aviso y crítico. Si todo es rojo, nada lo es.

- Lo crítico se ve primero, arriba y a la izquierda, con más tamaño.
- El orden es explícito: **severidad → número de afectados → antigüedad**.
- Nunca se comunica solo con color: cada estado lleva también icono y texto (accesibilidad, por ejemplo daltonismo).
- Los indicadores en estado correcto se mantienen visibles pero discretos.

### Pilar 2. Reducción de ruido operativo

Los eventos repetitivos se agrupan en un único incidente procesable, con una regla clara y comprobable:

> Mismo tipo de evento + mismo origen + misma ventana de tiempo = **un incidente**, con contador y primera y última aparición.

Ejemplo: 200 intentos de acceso fallidos desde un mismo origen en 10 minutos aparecen como una sola tarjeta ("200 intentos fallidos, origen X, 10 min"), no como 200 filas.

Para que no se pierda información, el detalle se puede expandir y los eventos originales siguen disponibles.

### Pilar 3. Acción guiada, sin ejecución (Actionable UX en solo lectura)

La idea original era añadir botones de contención directa (`Aislar Host`, `Bloquear IP`). Se descarta a propósito: el requisito 6 exige que el panel no modifique el sistema, y un botón que actúa sobre él pediría permisos de escritura, que la prueba señala como señal de alerta.

En su lugar, cada alerta crítica muestra:

1. **Qué ha pasado**, en una frase.
2. **Por qué importa**, el riesgo en lenguaje claro.
3. **Qué hacer ahora**, con el paso recomendado y el comando o procedimiento listo para **copiar**, que el analista ejecuta por su cuenta y con su autorización.
4. **Cómo confirmar** que fue un incidente real y no un falso positivo.

Así se mantiene el beneficio de reducir el tiempo de respuesta (qué hago y cómo, sin buscarlo) sin romper la regla de solo lectura. Esto se defiende en la memoria como decisión de diseño, no como carencia.

---

## 4. Estructura de la pantalla

Se lee de arriba abajo, en el orden en que el analista toma decisiones.

```
┌──────────────────────────────────────────────────────────────┐
│ ESTADO GLOBAL:  ● 2 críticos   ▲ 3 avisos   ✓ 4 correctos    │
│ Última actualización: 18:30:12            [ ⟳ Refrescar ]    │
├──────────────────────────────────────────────────────────────┤
│ ATENCIÓN INMEDIATA (solo críticos)                           │
│ ┌──────────────────────────────────────────────────────────┐ │
│ │ ● CRÍTICO · Servicio expuesto a Internet                 │ │
│ │ Qué pasa · Por qué importa · Qué hacer · Cómo confirmar  │ │
│ └──────────────────────────────────────────────────────────┘ │
├──────────────────────────────────────────────────────────────┤
│ INDICADORES  (cada uno con estado + justificación)           │
│ [Accesos fallidos] [Puertos] [Actualizaciones] [Cuentas] ... │
├──────────────────────────────────────────────────────────────┤
│ DETALLE / EVENTOS AGRUPADOS (se expande bajo demanda)        │
└──────────────────────────────────────────────────────────────┘
```

Reglas de la pantalla:

- Divulgación progresiva: primero el resumen y el detalle solo si se pide.
- Cada tarjeta responde a las tres preguntas del analista: qué es, qué tan grave es y qué hago.
- La hora de la última actualización y el botón de refrescar están siempre visibles (requisito 4).
- Estado vacío explícito: si no hay críticos, se dice "Sin alertas críticas", en lugar de dejar un hueco que confunda.

---

## 5. Fuentes de datos

El requisito pide al menos 2 fuentes distintas. Es mejor dos fuentes distintas que una muy detallada. Los ejemplos de abajo son orientativos y deben adaptarse al sistema que te asignen.

| Fuente | Qué aporta | Ejemplos de datos |
|---|---|---|
| Registros de autenticación | Quién intenta entrar y cómo | Intentos fallidos, accesos de cuentas privilegiadas |
| Estado de red y servicios | Superficie expuesta | Puertos en escucha, servicios abiertos a todas las interfaces |
| Estado de parches (opcional) | Exposición a vulnerabilidades | Actualizaciones de seguridad pendientes |

El panel solo **lee** estas fuentes. Si una no está disponible, el panel lo indica como "no evaluable" en lugar de mostrar un falso "correcto".

---

## 6. Indicadores y reglas de detección

Al menos 5 indicadores con estado (correcto, aviso o crítico) y su justificación. Valores orientativos: ajústalos al sistema real y defiéndelos.

| # | Indicador | Correcto | Aviso | Crítico | Justificación |
|---|---|---|---|---|---|
| 1 | Intentos de acceso fallidos (agrupados por origen) | Pocos y dispersos | Ráfaga desde un origen | Ráfaga seguida de un acceso correcto | Fuerza bruta que tuvo éxito es el peor caso |
| 2 | Servicios expuestos a todas las interfaces | Solo los esperados | Servicio no esperado en red interna | Servicio de administración abierto a Internet | Reduce superficie de ataque |
| 3 | Cuentas privilegiadas | Uso esperado | Acceso en horario inusual | Acceso privilegiado desde origen nuevo | Máximo impacto si se compromete |
| 4 | Parches de seguridad pendientes | Ninguno | Pendientes recientes | Pendientes críticos antiguos | El tiempo de exposición aumenta el riesgo |
| 5 | Frescura de los datos | Datos recientes | Datos algo desfasados | Fuente sin datos | Un panel con datos viejos da falsa seguridad |

Regla general: **cada alerta responde a una regla del tipo "esto expuesto = crítico"** y esa regla vive en una función pura y testeable.

---

## 7. Falsos positivos

El panel puede avisar sin que haya un problema real. Los casos previstos son:

- **Pruebas o escaneos autorizados** que generan ráfagas de intentos fallidos.
- **Usuario legítimo que olvida la contraseña** y falla varias veces.
- **Servicio expuesto a propósito**, por ejemplo un servidor web público.
- **Accesos de administración en horario inusual** por una guardia o un mantenimiento.
- **Datos desfasados** que parecen un incidente nuevo cuando solo son una lectura tardía.

Mitigaciones:

- Umbrales explícitos y documentados.
- Una lista de elementos esperados (*allowlist*) definida por el equipo, no por la IA.
- Cada alerta incluye "cómo confirmar" para que el analista descarte el falso positivo rápido.

---

## 8. Acción del analista ante cada alerta roja

| Alerta roja | Primer paso | Cómo confirmar | Siguiente paso |
|---|---|---|---|
| Ráfaga de fallos seguida de acceso correcto | Revisar la cuenta que accedió y la hora | Comparar con la actividad habitual del usuario | Escalar al responsable para forzar cambio de credenciales |
| Servicio de administración expuesto a Internet | Identificar quién lo abrió y por qué | Comprobar si estaba previsto | Solicitar el cierre o restringir el acceso por origen |
| Acceso privilegiado desde origen nuevo | Contactar con el titular de la cuenta | Verificar que reconoce el acceso | Si no lo reconoce, escalar como incidente |
| Fuente sin datos | Comprobar si el origen sigue activo | Revisar el último dato válido | Informar de la pérdida de visibilidad |

El panel **recomienda**; la acción sobre el sistema la ejecuta una persona con autorización.

---

## 9. Privacidad y datos sensibles

- Se anonimizan nombres de usuario, IPs y nombres de equipo en la vista: se muestran enmascarados (por ejemplo, `usr-****42`, `203.0.113.x`) o con un identificador estable no reversible.
- Misma entidad = mismo alias, para poder correlacionar sin exponer el dato real.
- No se guardan credenciales, y los permisos del panel son solo de lectura.
- Los tests y las capturas usan datos de ejemplo, nunca los del sistema en vivo.
- En la memoria se explica qué información se maneja y cómo se protege.

---

## 10. Pruebas

Mínimo 5 tests reales sobre las reglas, con datos de ejemplo, para que sean reproducibles.

1. Una ráfaga de fallos desde un origen se agrupa en **un** incidente.
2. Una ráfaga seguida de un acceso correcto se clasifica como **crítico**.
3. Un servicio de administración expuesto a Internet es **crítico**; el mismo en red interna es **aviso**.
4. Un servicio en la lista de esperados **no** genera alerta (falso positivo evitado).
5. Una fuente sin datos produce **"no evaluable"**, nunca "correcto".

Extras recomendables: el orden de prioridad (severidad, afectados, antigüedad) y que la anonimización es consistente.

---

## 11. Cómo validar el diseño

Estas son pruebas que puedes proponer o hacer con una persona, sin afirmar resultados que no has medido:

- **Test de 5 segundos:** se enseña la pantalla 5 segundos y se pregunta "¿qué es lo más grave y qué harías?".
- **Tiempo hasta la primera acción:** cuánto tarda alguien en saber qué hacer ante una alerta roja.
- **Conteo de ruido:** número de filas con y sin agrupación para el mismo conjunto de eventos.

Estas medidas apoyan la afirmación de que el diseño reduce el tiempo de respuesta, en lugar de darlo por hecho.

---

## 12. Uso de la IA y errores corregidos

La memoria exige **al menos 2 casos reales** en que la IA se equivocó y cómo los corregiste. Anótalos mientras trabajas. Rellena con los tuyos usando este formato:

| Qué propuso la IA | Por qué era un error | Cómo lo corregí |
|---|---|---|
| *(tu caso real 1)* | | |
| *(tu caso real 2)* | | |

Pistas de dónde suelen fallar: proponer permisos de escritura, inventar un endpoint o campo que no existe, umbrales sin justificar, o tests que dependen del sistema en vivo.

---

## 13. Pendiente y mejoras futuras

- **Respuesta asistida con aprobación humana:** botones de contención (aislar host, bloquear IP) en una versión futura, con confirmación explícita, registro de auditoría y permisos separados. Fuera de alcance aquí por el requisito de solo lectura.
- Comparar con ejecuciones anteriores para mostrar "qué ha cambiado".
- Priorización con contexto de negocio (qué activos son más importantes).
- Notificaciones para críticos nuevos.

---

## 14. Conclusión

> "En definitiva, una herramienta de ciberseguridad excelente no es la que muestra más datos en pantalla, sino la que permite al equipo **tomar la decisión correcta en el menor tiempo posible**, sin salirse de los límites de lo que se le ha autorizado a observar."

---

## Checklist de entrega

- [ ] Repositorio con el código
- [ ] `README.md` con instrucciones para ejecutarlo
- [ ] Memoria con las 6 preguntas respondidas (elección, prioridad, falsos positivos, privacidad, acción del analista, errores de la IA)
- [ ] 5 o más tests con datos de ejemplo
- [ ] Hora de última actualización y botón de refrescar
- [ ] Datos de al menos 2 fuentes reales, anonimizados
- [ ] Sin secretos en el repositorio
