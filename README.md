# 📸 Objetivo DX — Backend Architecture & API Specification

[![Spring Boot](https://img.shields.io/badge/Backend-Java%20%2F%20Spring%20Boot-brightgreen.svg)]()
[![Database](https://img.shields.io/badge/Database-MySQL%20%2F%20Relational-blue.svg)]()
[![Architecture](https://img.shields.io/badge/Architecture-REST%20API%20Layered-orange.svg)]()
[![Status](https://img.shields.io/badge/Project%20Status-Fases%201%20a%203-blueviolet.svg)]()

> Plataforma web de gestión integral y control de cobros para la escuela de fotografía artística **Objetivo DX**, diseñada en colaboración con **Zaitec** como proyecto técnico formativo del ciclo **Técnico Superior en Desarrollo de Aplicaciones Web (DAW)**.

---

## 📌 Contexto y Aviso de Confidencialidad

Este repositorio tiene fines **demostrativos y de portafolio técnico**. Contiene la especificación de requisitos, arquitectura backend, diseño de base de datos, endpoints de la API REST y flujos de negocio del proyecto.

> 🔒 **Nota de confidencialidad:** El código fuente completo de producción es propiedad de la empresa colaboradora (**Zaitec**) y se encuentra alojado en su repositorio privado institucional en cumplimiento de los acuerdos de confidencialidad y propiedad intelectual.

---

## 📄 Documentación Técnica Asociada

Se incluye la documentación original de definición de requisitos y planificación inicial en el directorio `/docs`:
* 📑 **Solicitud de Proyecto y Requisitos Funcionales:** [`/docs/Proyecto_GS02.pdf`](./docs/Proyecto_GS02.pdf)
* 📋 **Planificación y Tareas Iniciales (9 Semanas):** [`/docs/Tareas_Iniciales_Proyecto_Web.pdf`](./docs/Tareas_Iniciales_Proyecto_Web.pdf)

---

## 🏢 Problemática de Negocio

La escuela de fotografía **Objetivo DX** imparte formación especializada (fotografía, iluminación, edición y lenguaje visual) a una comunidad creciente de estudiantes. Con el incremento de alumnado y la diversificación de métodos de abono (transferencia bancaria, Bizum, efectivo), la gestión manual mediante mensajería y hojas de cálculo generaba:
* Pérdida significativa de tiempo administrativo en tareas operativas repetitivas.
* Alto riesgo de error humano al contrastar transferencias y recibos bancarios.
* Dificultad para disponer de una visión clara y en tiempo real del estado de cobros de las cuotas mensuales y anuales.

---

## 🎯 Objetivos del Sistema

* Centralizar y monitorizar en tiempo real los cobros realizados por cada estudiante.
* Identificar con precisión el método de pago empleado (transferencia, Bizum o efectivo).
* Diferenciar claramente entre matrículas de modalidad mensual y pagos de cuota anual.
* Reducir la carga administrativa manual y minimizar discrepancias contables.
* Evolucionar el sistema de forma modular hacia la conciliación bancaria y la facturación legal automatizada conforme a la normativa española.

---

## 👥 Tipos de Usuario y Roles

* **👨‍🎓 Alumno:**
  * Registro de usuario e inicio de sesión seguro.
  * Consulta personal del estado de sus pagos y cuotas.
  * Registro y notificación asistida de pagos realizados.

* **👨‍💼 Administrador (Profesor / Gestor):**
  * Acceso al panel de control integral.
  * Visualización global del listado de alumnos y sus cobros asociados.
  * Validación, edición y filtrado de pagos (pendientes, validados, mensuales, anuales).
  * Gestión de conciliación bancaria y generación/descarga de facturas legales en PDF.

---

## 🚀 Requisitos Funcionales por Fases

### Fase 1 — Registro manual asistido por el alumno
* **Objetivo:** Crear un sistema básico donde los alumnos informan de sus pagos manualmente.
* **Autenticación:**
  * Registro de usuario mediante email y contraseña.
  * Inicio de sesión y gestión básica de sesiones.
* **Perfil de alumno:**
  * Registro de datos mínimos: nombre, email y tipo de matrícula (mensual/anual).
* **Registro de pagos:**
  * Creación de un nuevo registro de pago por parte del alumno.
  * Introducción de datos: fecha del pago, método (transferencia, Bizum, efectivo, etc.), tipo de pago (mensual o anual) e importe opcional.
* **Panel de administrador:**
  * Visualización del listado completo de alumnos y pagos vinculados.
  * Filtros por estado (pagados / no pagados) y tipo de cuota.
  * Marcado de estados de pago como "Pendiente" o "Validado".
* **Modelo de datos básico (orientativo):**
  * **Usuario:** `id`, `nombre`, `email`, `contraseña`, `rol` (alumno/admin).
  * **Pago:** `id`, `usuario_id`, `fecha`, `metodo_pago`, `tipo_pago` (mensual/anual), `estado` (pendiente/validado).

---

### Fase 2 — Automatización parcial de pagos
* **Objetivo:** Reducir la dependencia del registro manual.
* **Importación de movimientos bancarios:**
  * Subida de extractos bancarios en archivo CSV o Excel.
  * Parseo automático de campos: fecha, concepto e importe.
* **Asociación automática:**
  * Detección del alumno a partir del concepto bancario o importe (ej. *"Pago Juan Pérez marzo"*).
* **Validación supervisada:**
  * Revisión de sugerencias automáticas por parte del administrador.
  * Capacidad para confirmar la asociación o editar manualmente los datos.
* **Mejoras de UX:**
  * Alertas ante cobros huérfanos o no identificados.
  * Indicadores visuales mediante código de colores y estados.

---

### Fase 3 — Facturación automática conforme a normativa española
* **Objetivo:** Cumplir con las obligaciones fiscales de un profesional autónomo.
* **Generación de facturas:**
  * Generación automática en el momento en que se valida un pago.
  * Inclusión de datos reglamentarios: número de factura, fecha, datos del emisor (profesor), datos del cliente (alumno), base imponible e IVA aplicado.
* **Cumplimiento legal en España:**
  * Numeración correlativa estricta.
  * Cálculo y desglose preciso del IVA correspondiente.
* **Almacenamiento y exportación:**
  * Exportación y descarga de facturas en formato PDF.
  * Consulta del histórico completo de facturas generadas.

---

## 🏗️ Arquitectura de Software

La aplicación backend se estructura siguiendo una arquitectura en capas desacoplada para garantizar modularidad, mantenibilidad y separación de responsabilidades:

```mermaid
graph TD
    Client[Cliente / Frontend Web] -->|HTTP Requests / JSON| Controller[Capa Controladores / REST API]
    Controller -->|DTOs y Validación| Service[Capa de Servicio / Lógica de Negocio]
    Service -->|Entidades JPA| Repository[Capa Repositorio / Spring Data JPA]
    Repository -->|SQL / JDBC| DB[(Base de Datos Relacional)]
