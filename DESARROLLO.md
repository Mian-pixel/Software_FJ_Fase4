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


class Servicio(ABC):
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

# =====================================================
# 8. FUNCIONES PRINCIPALES Y PRUEBAS
# DESARROLLADO POR: Oscar Fernando Hincapié Hernández
# =====================================================

import datetime

def registrar_log(mensaje):
    """Guarda errores y eventos en el archivo de logs."""
    try:
        with open("errores.log", "a", encoding="utf-8") as archivo:
            hora = datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
            archivo.write(f"[{hora}] {mensaje}\n")
    except Exception as e:
        print(f"No se pudo escribir en el log: {e}")

def ejecutar_pruebas():
    print("="*55)
    print("  SOFTWARE FJ - PRUEBA DE EJECUCION DEL SISTEMA")
    print("  FASE 4 - MANEJO DE EXCEPCIONES")
    print("="*55)
    registrar_log("Inicio de pruebas del sistema")

    # 1. Cliente valido
    try:
        c1 = Cliente("123", "Fernando Hincapie", "fernando@gmail.com", "3001234567")
        print("\n[1] OK Cliente valido creado:")
        print(c1.mostrar_informacion())
        registrar_log("Cliente valido creado: Fernando Hincapie")
    except ErrorSistema as e:
        print(f"\n[1] ERROR: {e}")
        registrar_log(f"Error prueba 1: {e}")
        c1 = None
    else:
        print("-> Prueba 1 superada sin errores.")
    finally:
        print("-> Fin prueba 1.")

    # 2. Cliente invalido - correo sin @
    try:
        c_malo = Cliente("124", "Cliente Malo", "correo-sin-arroba", "3001234567")
        print("\n[2] Cliente creado (no deberia pasar)")
    except DatosInvalidosError as e:
        print(f"\n[2] OK Error controlado - Cliente invalido: {e}")
        registrar_log(f"Error controlado prueba 2: {e}")
    except ErrorSistema as e:
        print(f"\n[2] ERROR GENERAL: {e}")

    # 3. Cliente invalido - telefono con letras
    try:
        c_malo2 = Cliente("125", "Otro Cliente", "otro@gmail.com", "300ABC")
        print("\n[3] Cliente creado (no deberia pasar)")
    except DatosInvalidosError as e:
        print(f"\n[3] OK Error controlado - Telefono invalido: {e}")
        registrar_log(f"Error controlado prueba 3: {e}")

    # 4. Servicios validos + polimorfismo
    try:
        sala = ReservaSala("SALA-01", 20)
        equipo = AlquilerEquipo("EQ-01", "Video Beam")
        asesoria = AsesoriaEspecializada("ASE-01", "Programacion Python")
        print(f"\n[4] OK Servicios creados:")
        print(" ", sala.describir_servicio(), "- Costo 2h: $", sala.calcular_costo(2))
        print(" ", equipo.describir_servicio(), "- Costo 3h: $", equipo.calcular_costo(3))
        print(" ", asesoria.describir_servicio(), "- Costo 1h: $", asesoria.calcular_costo(1))
        # Sobrecarga simulada: costo con impuesto y descuento
        print("  Sala 2h con impuesto 19% y descuento 10%: $", f"{sala.calcular_costo_con_impuesto(2, 0.19, 0.10):,.0f}")
        registrar_log("Servicios validos creados")
    except ErrorSistema as e:
        print(f"\n[4] ERROR: {e}")
        registrar_log(f"Error prueba 4: {e}")

    # 5. Reserva exitosa
    try:
        r1 = Reserva("R-001", c1, sala, 2)
        print("\n[5] OK Reserva creada:", r1.mostrar_reserva())
        r1.confirmar_reserva()
        print("  Estado actual:", r1.estado)
        registrar_log("Reserva R-001 confirmada")
    except ErrorSistema as e:
        print(f"\n[5] ERROR: {e}")
        registrar_log(f"Error prueba 5: {e}")

    # 6. Reserva invalida - duracion 0
    try:
        r_mala = Reserva("R-002", c1, sala, 0)
        print("\n[6] Reserva creada (no deberia pasar)")
    except ReservaInvalidaError as e:
        print(f"\n[6] OK Error controlado - Duracion invalida: {e}")
        registrar_log(f"Error controlado prueba 6: {e}")

    # 7. Reserva invalida - cliente no es Cliente
    try:
        r_mala2 = Reserva("R-003", "Juan sin registrar", sala, 2)
        print("\n[7] Reserva creada (no deberia pasar)")
    except ClienteNoEncontradoError as e:
        print(f"\n[7] OK Error controlado - Cliente no valido: {e}")
        registrar_log(f"Error controlado prueba 7: {e}")

    # 8. Servicio no disponible
    try:
        sala_ocupada = ReservaSala("SALA-02", 10)
        sala_ocupada.disponible = False
        r3 = Reserva("R-004", c1, sala_ocupada, 1)
        r3.confirmar_reserva()
        print("\n[8] Reserva confirmada (no deberia pasar)")
    except ServicioNoDisponibleError as e:
        print(f"\n[8] OK Error controlado - Servicio no disponible: {e}")
        registrar_log(f"Error controlado prueba 8: {e}")
    except ErrorSistema as e:
        print(f"\n[8] ERROR: {e}")

    # 9. Operacion no permitida - confirmar 2 veces
    try:
        r1.confirmar_reserva()
        print("\n[9] Confirmada 2 veces (no deberia pasar)")
    except OperacionNoPermitidaError as e:
        print(f"\n[9] OK Error controlado - Confirmar 2 veces: {e}")
        registrar_log(f"Error controlado prueba 9: {e}")

    # 10. Cancelar reserva + encadenamiento de excepciones
    try:
        try:
            costo = sala.calcular_costo_con_impuesto(-5)
        except Exception as e:
            raise ReservaInvalidaError("Fallo al calcular costo en prueba 10") from e
    except ReservaInvalidaError as e:
        print(f"\n[10] OK Error encadenado controlado: {e}")
        print(f"     Causa original: {e.__cause__}")
        registrar_log(f"Error encadenado prueba 10: {e} | Causa: {e.__cause__}")

    try:
        r1.cancelar_reserva()
        print(f"\n[10b] OK Reserva R-001 cancelada. Estado: {r1.estado}")
        r1.cancelar_reserva()
    except OperacionNoPermitidaError as e:
        print(f"[10b] OK Error controlado - Cancelar 2 veces: {e}")
        registrar_log(f"Error controlado prueba 10b: {e}")

    print("\n" + "="*55)
    print("  FIN DE PRUEBAS - EL SISTEMA SIGUIO FUNCIONANDO")
    print("  Revisa el archivo errores.log con todos los eventos")
    print("="*55)
    registrar_log("Fin de pruebas - sistema estable")

if __name__ == "__main__":
    ejecutar_pruebas()
