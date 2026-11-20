# =====================================================
# PROYECTO: SOFTWARE FJ
# CURSO: PROGRAMACIÓN - UNAD
# FASE 4
# DESCRIPCIÓN: Sistema de gestión de clientes,
# servicios y reservas.
# GRUPO:213023_140
# INTEGRANTES: 
# -MIGUEL ANGEL CASTAÑEDA
# -Oscar Fernando Hincapié Hernández
# -
# -
# -
# =====================================================


# =====================================================
# 1. IMPORTACIÓN DE LIBRERÍAS
# =====================================================

from abc import ABC, abstractmethod


# =====================================================
# 2. EXCEPCIONES PERSONALIZADAS
# DESARROLLADO POR:MIGUEL ANGEL CASTAÑEDA
# =====================================================
class ErrorSistema(Exception):
    """Excepción general del sistema Software FJ."""
    pass


class DatosInvalidosError(ErrorSistema):
    """Se genera cuando los datos ingresados son incorrectos."""
    pass


class ClienteNoEncontradoError(ErrorSistema):
    """Se genera cuando no se encuentra un cliente."""
    pass


class ServicioNoDisponibleError(ErrorSistema):
    """Se genera cuando un servicio no está disponible."""
    pass


class ReservaInvalidaError(ErrorSistema):
    """Se genera cuando una reserva no cumple las condiciones."""
    pass


class OperacionNoPermitidaError(ErrorSistema):
    """Se genera cuando una operación no está permitida."""
    pass


# =====================================================
# 3. CLASE ABSTRACTA ENTIDAD
# DESARROLLADO POR: Miguel Angel Castañeda
# =====================================================

class Entidad(ABC):
    """
    Clase abstracta que representa una entidad general
    del sistema Software FJ.
    """

    def __init__(self, identificacion, nombre):
        self._identificacion = identificacion
        self._nombre = nombre

    @property
    def identificacion(self):
        return self._identificacion

    @property
    def nombre(self):
        return self._nombre

    @abstractmethod
    def mostrar_informacion(self):
        """
        Método abstracto que deberán implementar
        las clases que hereden de Entidad.
        """
        pass


# =====================================================
# 4. CLASE CLIENTE
# DESARROLLADO POR: Miguel Angel Castañeda
# =====================================================


class Cliente(Entidad):
    """
    Representa un cliente de la empresa Software FJ.

    Hereda los atributos de identificación y nombre
    de la clase abstracta Entidad.
    """

    def __init__(self, identificacion, nombre, correo, telefono):

        # Inicializamos los atributos heredados
        super().__init__(identificacion, nombre)

        # Validamos la identificación
        if not identificacion or not identificacion.strip():
            raise DatosInvalidosError(
                "La identificación es obligatoria."
            )

        # Validamos el nombre
        if not nombre or not nombre.strip():
            raise DatosInvalidosError(
                "El nombre es obligatorio."
            )

        # Validamos el correo
        if not isinstance(correo, str) or "@" not in correo:
            raise DatosInvalidosError(
                "El correo electrónico no es válido."
            )

        # Validamos el teléfono
        if not isinstance(telefono, str) or not telefono.isdigit():
            raise DatosInvalidosError(
                "El teléfono debe contener solamente números."
            )

        # Encapsulamos los datos personales
        self.__correo = correo
        self.__telefono = telefono

    @property
    def correo(self):
        return self.__correo

    @property
    def telefono(self):
        return self.__telefono

    def mostrar_informacion(self):
        """
        Devuelve la información completa del cliente.
        """

        return (
            f"Identificación: {self.identificacion}\n"
            f"Nombre: {self.nombre}\n"
            f"Correo: {self.correo}\n"
            f"Teléfono: {self.telefono}"
        )



# =====================================================
# 5. CLASE ABSTRACTA SERVICIO
# DESARROLLADO POR: Oscar Fernando Hincapié Hernández
# =====================================================


lass Servicio(ABC):
    """Clase abstracta base para servicios de Software FJ"""
    def __init__(self, codigo, nombre, disponible=True):
        self.codigo = codigo
        self.nombre = nombre
        self.disponible = disponible

    @abstractmethod
    def calcular_costo(self, duracion):
        pass

    @abstractmethod
    def describir_servicio(self):
        pass

    # Sobrecarga simulada con parametros opcionales
    def calcular_costo_con_impuesto(self, duracion, impuesto=0.19, descuento=0):
        try:
            costo = self.calcular_costo(duracion)
            return costo * (1 + impuesto) * (1 - descuento)
        except Exception as e:
            raise ReservaInvalidaError(f"Error calculando costo con impuesto") from e

    def validar_disponibilidad(self):
        if not self.disponible:
            raise ServicioNoDisponibleError(f"Servicio {self.codigo} no disponible")
            

# =====================================================
# 6. SERVICIOS ESPECIALIZADOS
# DESARROLLADO POR: Oscar Fernando Hincapié Hernández
# =====================================================


class ReservaSala(Servicio):
    def __init__(self, codigo, capacidad):
        super().__init__(codigo, f"Sala capacidad {capacidad}")
        self.capacidad = capacidad
    def calcular_costo(self, duracion):
        if duracion <=0: raise DatosInvalidosError("Duracion invalida")
        return 50000 * duracion
    def describir_servicio(self):
        return f"[Sala] {self.codigo} - Capacidad: {self.capacidad}"

class AlquilerEquipo(Servicio):
    def __init__(self, codigo, tipo_equipo):
        super().__init__(codigo, f"Equipo {tipo_equipo}")
        self.tipo = tipo_equipo
    def calcular_costo(self, duracion):
        return 30000 * duracion
    def describir_servicio(self):
        return f"[Equipo] {self.codigo} - Tipo: {self.tipo}"

class AsesoriaEspecializada(Servicio):
    def __init__(self, codigo, especialidad):
        super().__init__(codigo, f"Asesoria {especialidad}")
        self.especialidad = especialidad
    def calcular_costo(self, duracion):
        return 80000 * duracion
    def describir_servicio(self):
        return f"[Asesoria] {self.codigo} - {self.especialidad}"
        

# =====================================================
# 7. CLASE RESERVA
# DESARROLLADO POR: 
# =====================================================
class Reserva:
    """
    Clase encargada de gestionar las reservas
    de clientes y servicios de Software FJ.

    Atributos:
        codigo: Identificador de la reserva.
        cliente: Cliente que realiza la reserva.
        servicio: Servicio seleccionado.
        duracion: Duración del servicio.
        estado: Estado actual de la reserva.
    """

    def __init__(self, codigo, cliente, servicio, duracion):

        # Validar el código de la reserva
        if not isinstance(codigo, str) or not codigo.strip():
            raise ReservaInvalidaError(
                "El código de la reserva es obligatorio."
            )

        # Validar el cliente
        if not isinstance(cliente, Cliente):
            raise ClienteNoEncontradoError(
                "El cliente ingresado no es válido."
            )

        # Validar la duración
        if (isinstance(duracion, bool)
                or not isinstance(duracion, (int, float))
                or duracion <= 0):
            raise ReservaInvalidaError(
                "La duración debe ser mayor que cero."
            )

        # Validar que el servicio permita calcular costos
        if not isinstance(servicio, Servicio):
            raise ReservaInvalidaError(
                "El servicio seleccionado no es válido."
            )

        # Asignación de atributos
        self.codigo = codigo
        self.cliente = cliente
        self.servicio = servicio
        self.duracion = duracion
        self.estado = "Pendiente"

        # Calcular el costo de la reserva
        self.costo = servicio.calcular_costo(duracion)

    def confirmar_reserva(self):
        """
        Confirma una reserva cuando el servicio
        se encuentra disponible.
        """

        if self.estado != "Pendiente":
            raise OperacionNoPermitidaError(
                "Solo se pueden confirmar reservas pendientes."
            )

        self.servicio.validar_disponibilidad()

        self.estado = "Confirmada"

        print(f"Reserva {self.codigo} confirmada correctamente.")

    def cancelar_reserva(self):
        """
        Cancela una reserva que no haya sido cancelada.
        """

        if self.estado == "Cancelada":
            raise OperacionNoPermitidaError(
                "La reserva ya se encuentra cancelada."
            )

        self.estado = "Cancelada"

        print(f"Reserva {self.codigo} cancelada correctamente.")

    def mostrar_reserva(self):
        """
        Muestra la información completa de la reserva.
        """

        return (
            f"\n--- INFORMACIÓN DE LA RESERVA ---\n"
            f"Código: {self.codigo}\n"
            f"Cliente: {self.cliente.nombre}\n"
            f"Servicio: {self.servicio.nombre}\n"
            f"Duración: {self.duracion}\n"
            f"Costo: ${self.costo:,.0f}\n"
            f"Estado: {self.estado}"
        )



# =====================================================
# 8. FUNCIONES PRINCIPALES Y PRUEBAS
# DESARROLLADO POR: 
# =====================================================


# Prueba EJECUCION DEL SISTEMA
