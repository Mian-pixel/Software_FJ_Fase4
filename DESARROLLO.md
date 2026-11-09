# =====================================================
# PROYECTO: SOFTWARE FJ
# CURSO: PROGRAMACIÓN - UNAD
# FASE 4
# DESCRIPCIÓN: Sistema de gestión de clientes,
# servicios y reservas.
# GRUPO:213023_140
# INTEGRANTES: 
# -MIGUEL ANGEL CASTAÑEDA
# -
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
# DESARROLLADO POR:
# =====================================================



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
# DESARROLLADO POR: 
# =====================================================




# =====================================================
# 6. SERVICIOS ESPECIALIZADOS
# DESARROLLADO POR: 
# =====================================================




# =====================================================
# 7. CLASE RESERVA
# DESARROLLADO POR: 
# =====================================================




# =====================================================
# 8. FUNCIONES PRINCIPALES Y PRUEBAS
# DESARROLLADO POR: 
# =====================================================


# Prueba EJECUCION DEL SISTEMA
