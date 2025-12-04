#include <iostream>
#include <string>
#include <vector>

using std::string;
using std::cout;
using std::endl;
using std::vector;

class HistorialMedico {
private:
    string vacunas;
    string observaciones;
public:
    HistorialMedico() : vacunas(""), observaciones("") {}
    HistorialMedico(const string& v, const string& o) : vacunas(v), observaciones(o) {}

    void setVacunas(const string& v) { vacunas = v; }
    void setObservaciones(const string& o) { observaciones = o; }

    string getVacunas() const { return vacunas; }
    string getObservaciones() const { return observaciones; }

    string resumen() const {
        return "Vacunas: " + (vacunas.empty() ? "N/A" : vacunas) + " | Observaciones: " + (observaciones.empty() ? "N/A" : observaciones);
    }
};

class Animal {
protected:
    string nombre;
    int edad;
    string especie;
    HistorialMedico historial;

public:
    Animal(const string& nombre_, int edad_, const string& especie_)
        : nombre(nombre_), edad(edad_), especie(especie_), historial() {}

    virtual ~Animal() {}

    string getNombre() const { return nombre; }
    int getEdad() const { return edad; }
    string getEspecie() const { return especie; }

    void setVacunas(const string& v) { historial.setVacunas(v); }
    void setObservaciones(const string& o) { historial.setObservaciones(o); }
    string getVacunas() const { return historial.getVacunas(); }
    string getObservaciones() const { return historial.getObservaciones(); }
    string getResumenHistorial() const { return historial.resumen(); }

    virtual void hacerSonido() const = 0;

    virtual void mostrarInfo() const {
        cout << "Nombre: " << nombre << " | Edad: " << edad << " | Especie: " << especie << endl;
        cout << "  " << getResumenHistorial() << endl;
    }
};

class Perro : public Animal {
private:
    string raza;
public:
    Perro(const string& nombre_, int edad_, const string& raza_)
        : Animal(nombre_, edad_, "Perro"), raza(raza_) {}

    void hacerSonido() const override {
        cout << nombre << " dice: ¡Guau!" << endl;
    }

    string getRaza() const { return raza; }

    void mostrarInfo() const override {
        cout << "[Perro] ";
        Animal::mostrarInfo();
        cout << "  Raza: " << raza << endl;
    }
};

class Gato : public Animal {
private:
    bool indoor;
public:
    Gato(const string& nombre_, int edad_, bool indoor_)
        : Animal(nombre_, edad_, "Gato"), indoor(indoor_) {}

    void hacerSonido() const override {
        cout << nombre << " dice: ¡Miau!" << endl;
    }

    bool isIndoor() const { return indoor; }

    void mostrarInfo() const override {
        cout << "[Gato] ";
        Animal::mostrarInfo();
        cout << "  Indoor: " << (indoor ? "Sí" : "No") << endl;
    }
};

class Veterinario {
private:
    string nombre;
    int experiencia;
public:
    Veterinario(const string& nombre_, int experiencia_) : nombre(nombre_), experiencia(experiencia_) {}

    void revisarAnimal(Animal* a) const {
        if (!a) {
            cout << "No se proporcionó un animal para revisar." << endl;
            return;
        }
        cout << "Veterinario " << nombre << " (exp: " << experiencia << " años) revisa a " << a->getNombre() << ":" << endl;
        a->mostrarInfo();
        cout << "  -> Revisión terminada." << endl;
    }
};

class Refugio {
private:
    string nombre;
    string direccion;
    vector<Animal*> listaAnimales;

public:
    Refugio(const string& nombre_, const string& direccion_) : nombre(nombre_), direccion(direccion_) {}

    void agregarAnimal(Animal* a) {
        if (!a) {
            cout << "No se puede agregar un animal nulo." << endl;
            return;
        }
        listaAnimales.push_back(a);
        cout << "Agregado al refugio: " << a->getNombre() << endl;
    }

    void listarAnimales() const {
        cout << "Refugio: " << nombre << " | Dirección: " << direccion << endl;
        cout << "Animales registrados (" << listaAnimales.size() << "):" << endl;
        for (const auto& a : listaAnimales) {
            if (a) {
                cout << " - " << a->getNombre() << " (" << a->getEspecie() << ")" << endl;
            }
        }
    }

    Animal* buscarPorNombre(const string& nombreBuscado) const {
        for (auto* a : listaAnimales) {
            if (a && a->getNombre() == nombreBuscado) return a;
        }
        return nullptr;
    }
};

int main() {
    cout << "=== Demo: Refugio de Animales ===" << endl;

    Perro* p1 = new Perro("Rex", 4, "Labrador");
    Gato* g1  = new Gato("Misu", 2, true);

    p1->setVacunas("Rabia, Parvovirus");
    p1->setObservaciones("Buena condición general.");

    g1->setVacunas("Triple felina");
    g1->setObservaciones("Leve alergia en orejas.");

    Refugio refugio("Refugio Amigo", "Calle Falsa 123");
    refugio.agregarAnimal(p1);
    refugio.agregarAnimal(g1);

    cout << endl;
    refugio.listarAnimales();
    cout << endl;

    Veterinario vet("Dr. López", 10);
    vet.revisarAnimal(p1);
    cout << endl;
    vet.revisarAnimal(g1);
    cout << endl;

    cout << "Prueba de sonidos:" << endl;
    vector<Animal*> todos = { p1, g1 };
    for (Animal* a : todos) {
        a->hacerSonido();
    }
    cout << endl;

    Animal* encontrado = refugio.buscarPorNombre("Misu");
    if (encontrado) {
        cout << "Se encontró a: " << encontrado->getNombre() << ". Mostrando info:" << endl;
        encontrado->mostrarInfo();
    } else {
        cout << "Animal no encontrado." << endl;
    }

    delete p1;
    delete g1;

    cout << endl << "Fin del demo." << endl;
    return 0;
}
