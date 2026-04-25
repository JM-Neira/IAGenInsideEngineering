## solucion 

# prompt primer ejercicio (usando claude)
necesito ayuda con el siguiente ejercicio de programacion a continuacion de dare el contexto del problema, la mision y el objetivo: 
 
📝 Problema #1: El Videoclub de Don
Don Mario acaba de abrir un videoclub moderno donde los clientes pueden alquilar peliculas fisicas o digitales. El problema es que su sistema anterior era un caos: todos los precios se calculaban igual sin importar el tipo de pelicula o membresia del cliente, y no habia forma de saber que peliculas estaban disponibles en tiempo real.
Tu Mision
Ayuda a Don Mario creando un sistema de alquiler que permita:

1. Registrar peliculas (fisicas o digitales) con su disponibilidad.
2. Que el cliente elija X peliculas para alquilar.
3. Calcular el precio total segun su tipo de membresia:
   * Basica: precio normal.
   * Premium: 20% de descuento.
4. Mostrar al finalizar un recibo con las peliculas, precio por unidad y total.
Peliculas Disponibles

* [Fisica] Interestellar - $8.000 - Disponible
* [Fisica] El Padrino - $7.000 - No disponible
* [Digital] Inception - $5.000 - Disponible
* [Digital] Matrix - $6.000 - Disponible
Caso de Ejemplo
Membresia del cliente: Premium Seleccione peliculas (numeros separados por coma): 1,3

```
--- RECIBO DE ALQUILER ---
Cliente: Premium
Peliculas:
 - Interestellar (Fisica) - $8.000
 - Inception (Digital) - $5.000
Subtotal: $13.000
Descuento (20%): $2.600
Total a pagar: $10.400
--------------------------
¡Disfrute su pelicula!

```

Objetivos del Ejercicio

* Identificar cual o cuales patrones de diseno utilizar.
* Explicar que principios de SOLID se aplican.
* Aplicar polimorfismo y encapsulamiento.
* Colocar evidencia de la ejecucion del ejercicio (ejecucion por consola; no es necesario hacer front).

# respuesta generada por la ia(claude)

12:36 PMClaude responded: Voy a leer el skill de frontend-design antes de empezar, aunque este ejercicio es de consola.Voy a leer el skill de frontend-design antes de empezar, aunque este ejercicio es de consola. En realidad, es un ejercicio de programación pura — no necesito ese skill. Déjame analizar el problema y resolverlo.
Análisis del Diseño
Patrones de Diseño aplicables:
1. Strategy Pattern — para el cálculo de precios según membresía
2. Factory Method — para crear películas (Física o Digital)
3. Template Method — para el recibo (estructura común, detalles variables)
Principios SOLID aplicados:

S — Movie, PricingStrategy, Receipt tienen responsabilidad única
O — Nuevas membresías o tipos de película sin modificar clases existentes
L — PhysicalMovie y DigitalMovie sustituyen a Movie sin romper nada
I — Interfaces pequeñas y específicas
D — Depender de abstracciones (Movie, PricingStrategy), no de implementaciones concretas
Sistema de videoclub de Don Mario con patrones de diseñoSistema de videoclub de Don Mario con patrones de diseñoAhora ejecutamos el programa simulando el caso de ejemplo del enunciado:Ejecutar el programa con el caso de ejemplo: membresía Premium, películas 1 y 3Ejecutar el programa con el caso de ejemplo: membresía Premium, películas 1 y 3Perfecto. Ejecutamos también casos adicionales para verificar robustez:Casos adicionales de pruebaCasos adicionales de pruebaTodo funciona. Aquí está el archivo:Presented filePresented file
Explicación completa
🧩 Patrones de Diseño aplicados
PatrónClase(s)Por quéStrategyPricingStrategy, BasicMembership, PremiumMembershipEl algoritmo de descuento cambia según la membresía, sin tocar el resto del sistemaFactory MethodMovieFactory, MembershipFactoryDelega la creación de objetos a un método centralizado; añadir tipo "VR" no rompe nadaTemplate MethodRentalReceipt, StandardReceiptEl esqueleto del recibo (header → cliente → películas → precios → footer) está fijo; los detalles son personalizables

🔷 Principios SOLID
S — Single Responsibility
MovieCatalog solo gestiona el catálogo. RentalReceipt solo genera el recibo. PricingStrategy solo calcula precios.
O — Open/Closed
Para agregar membresía "Gold" con 35%: creas GoldMembership(PricingStrategy) y la registras en MembershipFactory._registry. Cero cambios en código existente.
L — Liskov Substitution
PhysicalMovie y DigitalMovie sustituyen a Movie en cualquier punto sin romper el comportamiento esperado.
I — Interface Segregation
PricingStrategy tiene solo 3 métodos; Movie expone solo lo necesario via @property. Ninguna clase se ve obligada a implementar métodos que no usa.
D — Dependency Inversion
RentalReceipt.generate() recibe PricingStrategy (abstracción), no PremiumMembership (implementación concreta). El mismo principio aplica con Movie.

🎯 Polimorfismo y Encapsulamiento

Polimorfismo: movie.movie_type() devuelve "Física" o "Digital" según la instancia, sin ningún if/else en el código cliente.
Encapsulamiento: Los atributos _title, _price, _available son protegidos y se exponen solo mediante @property, impidiendo modificación directa desde fuera.

# codigo generado por la ia(claude)

"""
================================================================================
  VIDEOCLUB DON MARIO — Sistema de Alquiler
================================================================================
  Patrones de Diseño:
    1. Strategy   → Cálculo de precios por tipo de membresía
    2. Factory    → Creación de películas (Física / Digital)
    3. Template   → Generación del recibo

  Principios SOLID:
    S → Cada clase tiene una sola responsabilidad
    O → Abierto a nuevas membresías/tipos sin modificar código existente
    L → PhysicalMovie / DigitalMovie sustituyen a Movie sin romper nada
    I → Interfaces pequeñas (PricingStrategy, Movie)
    D → Depende de abstracciones, no de implementaciones concretas
================================================================================
"""

from abc import ABC, abstractmethod
from typing import List


# ══════════════════════════════════════════════════════════════════════════════
# STRATEGY PATTERN — Estrategias de precio según membresía
# ══════════════════════════════════════════════════════════════════════════════

class PricingStrategy(ABC):
    """Interfaz para las estrategias de descuento (Strategy Pattern)."""

    @abstractmethod
    def apply_discount(self, subtotal: float) -> float:
        pass

    @abstractmethod
    def discount_percentage(self) -> int:
        pass

    @abstractmethod
    def membership_name(self) -> str:
        pass


class BasicMembership(PricingStrategy):
    """Membresía Básica: sin descuento."""

    def apply_discount(self, subtotal: float) -> float:
        return subtotal  # Sin descuento

    def discount_percentage(self) -> int:
        return 0

    def membership_name(self) -> str:
        return "Básica"


class PremiumMembership(PricingStrategy):
    """Membresía Premium: 20% de descuento."""

    def apply_discount(self, subtotal: float) -> float:
        return subtotal * 0.80

    def discount_percentage(self) -> int:
        return 20

    def membership_name(self) -> str:
        return "Premium"


# ══════════════════════════════════════════════════════════════════════════════
# MODELO — Película base (Principio L de SOLID + Polimorfismo)
# ══════════════════════════════════════════════════════════════════════════════

class Movie(ABC):
    """Clase abstracta base para todos los tipos de película."""

    def __init__(self, title: str, price: float, available: bool):
        self._title = title          # Encapsulamiento: atributos protegidos
        self._price = price
        self._available = available

    @property
    def title(self) -> str:
        return self._title

    @property
    def price(self) -> float:
        return self._price

    @property
    def available(self) -> bool:
        return self._available

    @abstractmethod
    def movie_type(self) -> str:
        """Polimorfismo: cada subclase define su tipo."""
        pass

    def availability_label(self) -> str:
        return "Disponible" if self._available else "No disponible"

    def __str__(self) -> str:
        status = "✔" if self._available else "✘"
        return (f"[{self.movie_type()}] {self._title} "
                f"- ${self._price:,.0f} - {status} {self.availability_label()}")


class PhysicalMovie(Movie):
    """Película en formato físico (DVD/Blu-ray)."""

    def movie_type(self) -> str:
        return "Física"


class DigitalMovie(Movie):
    """Película en formato digital (streaming/descarga)."""

    def movie_type(self) -> str:
        return "Digital"


# ══════════════════════════════════════════════════════════════════════════════
# FACTORY METHOD — Fábrica de películas
# ══════════════════════════════════════════════════════════════════════════════

class MovieFactory:
    """
    Fábrica para crear instancias de Movie según el tipo.
    Principio O: añadir 'VR' o '4K' sin tocar las clases existentes.
    """

    _registry = {
        "fisica":   PhysicalMovie,
        "digital":  DigitalMovie,
    }

    @classmethod
    def create(cls, movie_type: str, title: str,
               price: float, available: bool) -> Movie:
        klass = cls._registry.get(movie_type.lower())
        if not klass:
            raise ValueError(f"Tipo de película desconocido: '{movie_type}'")
        return klass(title, price, available)

    @classmethod
    def register(cls, movie_type: str, klass):
        """Extensión sin modificación (Principio O)."""
        cls._registry[movie_type.lower()] = klass


# ══════════════════════════════════════════════════════════════════════════════
# TEMPLATE METHOD — Recibo de alquiler
# ══════════════════════════════════════════════════════════════════════════════

class RentalReceipt(ABC):
    """
    Template Method: define el esqueleto del recibo.
    Las subclases pueden personalizar secciones específicas.
    """

    def generate(self, membership: PricingStrategy, movies: List[Movie]) -> str:
        lines = []
        lines.append(self._header())
        lines.append(self._client_info(membership))
        lines.append(self._movie_list(movies))
        lines.append(self._pricing(membership, movies))
        lines.append(self._footer())
        return "\n".join(lines)

    # ── Pasos del template ────────────────────────────────────────────────────

    def _header(self) -> str:
        return "\n" + "─" * 36 + "\n   RECIBO DE ALQUILER — VideoClub\n" + "─" * 36

    def _client_info(self, membership: PricingStrategy) -> str:
        return f"Cliente:     Membresía {membership.membership_name()}"

    def _movie_list(self, movies: List[Movie]) -> str:
        lines = ["Películas:"]
        for m in movies:
            lines.append(f"  • {m.title} ({m.movie_type()}) ........... ${m.price:>8,.0f}")
        return "\n".join(lines)

    def _pricing(self, membership: PricingStrategy, movies: List[Movie]) -> str:
        subtotal = sum(m.price for m in movies)
        discount_amt = subtotal - membership.apply_discount(subtotal)
        total = membership.apply_discount(subtotal)

        lines = ["─" * 36]
        lines.append(f"Subtotal:                      ${subtotal:>8,.0f}")
        if membership.discount_percentage() > 0:
            lines.append(
                f"Descuento ({membership.discount_percentage()}%):               "
                f"-${discount_amt:>7,.0f}"
            )
        lines.append(f"{'TOTAL A PAGAR':.<24}${total:>8,.0f}")
        return "\n".join(lines)

    @abstractmethod
    def _footer(self) -> str:
        pass


class StandardReceipt(RentalReceipt):
    def _footer(self) -> str:
        return "─" * 36 + "\n  ¡Disfrute su(s) película(s)!\n" + "─" * 36


# ══════════════════════════════════════════════════════════════════════════════
# CATÁLOGO — Repositorio de películas disponibles
# ══════════════════════════════════════════════════════════════════════════════

class MovieCatalog:
    """
    Responsabilidad única (Principio S): gestionar el catálogo.
    Encapsula la lista interna y expone solo lo necesario.
    """

    def __init__(self):
        self._movies: List[Movie] = []

    def add(self, movie: Movie) -> None:
        self._movies.append(movie)

    def all_movies(self) -> List[Movie]:
        return list(self._movies)

    def get_available(self, indices: List[int]) -> List[Movie]:
        """Devuelve películas disponibles según índices (1-based)."""
        selected = []
        errors = []
        for i in indices:
            if i < 1 or i > len(self._movies):
                errors.append(f"  ✘ Número {i} fuera de rango.")
                continue
            movie = self._movies[i - 1]
            if not movie.available:
                errors.append(f"  ✘ '{movie.title}' no está disponible.")
                continue
            selected.append(movie)
        return selected, errors

    def display(self) -> None:
        print("\n📽  CATÁLOGO DE PELÍCULAS")
        print("─" * 50)
        for idx, movie in enumerate(self._movies, start=1):
            print(f"  {idx}. {movie}")
        print("─" * 50)


# ══════════════════════════════════════════════════════════════════════════════
# MEMBERSHIP FACTORY — Creación de membresías
# ══════════════════════════════════════════════════════════════════════════════

class MembershipFactory:
    _registry = {
        "1": BasicMembership,
        "2": PremiumMembership,
    }

    @classmethod
    def create(cls, choice: str) -> PricingStrategy:
        klass = cls._registry.get(choice)
        if not klass:
            raise ValueError("Opción de membresía inválida.")
        return klass()


# ══════════════════════════════════════════════════════════════════════════════
# APLICACIÓN PRINCIPAL
# ══════════════════════════════════════════════════════════════════════════════

def build_catalog() -> MovieCatalog:
    """Construye el catálogo inicial usando la fábrica."""
    catalog = MovieCatalog()
    data = [
        ("fisica",  "Interstellar",  8_000, True),
        ("fisica",  "El Padrino",    7_000, False),
        ("digital", "Inception",     5_000, True),
        ("digital", "Matrix",        6_000, True),
    ]
    for movie_type, title, price, available in data:
        catalog.add(MovieFactory.create(movie_type, title, price, available))
    return catalog


def select_membership() -> PricingStrategy:
    print("\n🎟  TIPO DE MEMBRESÍA")
    print("  1. Básica   (precio normal)")
    print("  2. Premium  (20% de descuento)")
    while True:
        choice = input("Seleccione membresía [1/2]: ").strip()
        try:
            return MembershipFactory.create(choice)
        except ValueError:
            print("  ⚠  Opción inválida. Intente de nuevo.")


def select_movies(catalog: MovieCatalog) -> List[Movie]:
    while True:
        raw = input("\nSeleccione películas (números separados por coma): ")
        try:
            indices = [int(x.strip()) for x in raw.split(",") if x.strip()]
            if not indices:
                print("  ⚠  Debe seleccionar al menos una película.")
                continue
            selected, errors = catalog.get_available(indices)
            if errors:
                for err in errors:
                    print(err)
            if selected:
                return selected
            print("  ⚠  Ninguna película válida seleccionada. Intente de nuevo.")
        except ValueError:
            print("  ⚠  Ingrese solo números separados por coma.")


def main():
    print("=" * 50)
    print("   🎬  VIDEOCLUB DON MARIO  🎬")
    print("=" * 50)

    catalog   = build_catalog()
    catalog.display()

    membership = select_membership()
    selected   = select_movies(catalog)

    receipt    = StandardReceipt()
    print(receipt.generate(membership, selected))


if __name__ == "__main__":
    main()

# link conversacion primer ejercicio 
https://claude.ai/chat/2b000eed-8fc2-4b3c-9c83-c1c7f2e4a34b

# 


