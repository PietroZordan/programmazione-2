# Esercizi
## Esercizio elezioni con [Iterator, Map, TreeSet]
```Java
import java.util.Random;
import it.univr.elezioni.Elezioni;
import it.univr.elezioni.ElezioniVincitore;
import it.univr.elezioni.Partito;
public class Main {
	public static void main(String[] args) {
		// creo cinque partiti
		Partito[] partiti = new Partito[] {
			new Partito("Pensionati"),
			new Partito("Felice"),
			new Partito("Floreale"),
			new Partito("Caotico"),
			new Partito("Bassotti")
		};
		// elezioni inizialmente vuote
		Elezioni elezioni = new ElezioniVincitore();
		// aggiungo dei voti casuali per i partiti
		Random random = new Random();
		for (Partito partito: partiti) {
			int voti = random.nextInt(10000);
			for (int i = 0; i < voti; i++)
				elezioni.vota(partito);
		}
		// stampo le elezioni con indicazione di vincitore
		System.out.println(elezioni);
	}
}
```
```Java
package it.univr.elezioni;
import java.util.HashMap;
import java.util.Iterator;
import java.util.Map;
import java.util.TreeSet;

public class Elezioni implements Iterable<VotiPerPartito> {
	private final Map<Partito, Integer> voti = new HashMap<>();

	public final void vota(Partito partito) {
		voti.put(partito, 1 + votiPer(partito));
	}
	@Override
	public String toString() {
		int numeroVotiEspressi = numeroVotiEspressi();
		int num = 1;
		String result = "";
		for (VotiPerPartito vpp: this)
			result += String.format("%d %15s: %5d voti (%.2f%%)\n",
					num++,
					vpp.partito,
					vpp.voti,
					100.0 * vpp.voti / numeroVotiEspressi);
		return result;
	}
	private int numeroVotiEspressi() {
		int sum = 0;
		for (int v: voti.values())
			sum += v;		
		return sum;
	}
	private int votiPer(Partito partito) {
		return voti.getOrDefault(partito, 0);
	}
	@Override
	public final Iterator<VotiPerPartito> iterator() {
		return new Iterator<VotiPerPartito>() {
			private final Iterator<Partito> it = new TreeSet<>(voti.keySet()).iterator();

			@Override
			public boolean hasNext() {
				return it.hasNext();
			}

			@Override
			public VotiPerPartito next() {
				Partito prossimoPartito = it.next();
				return new VotiPerPartito(prossimoPartito, votiPer(prossimoPartito));
			}
		};
	}
}
```

```Java
package it.univr.elezioni;
public class ElezioniVincitore extends Elezioni {

	@Override
	public String toString() {
		String result = super.toString() + "\n";

		VotiPerPartito vincitore = null;
		for (VotiPerPartito vpp: this)
			if (vincitore == null || vincitore.voti < vpp.voti)
				vincitore = vpp;

		if (vincitore == null)
			return result + "Non ci sono vincitori\n";
		else
			return result + "Vince " + vincitore.partito + "\n";
	}
}
```

```Java
package it.univr.elezioni;
public class Partito implements Comparable<Partito> {
	private final String nome;

	public Partito(String nome) {
		this.nome = nome;
	}

	@Override
	public int compareTo(Partito other) {
		return nome.compareTo(other.nome);	//return other.nome.compareTo(nome);
	}
	@Override
	public boolean equals(Object other) {
		return other instanceof Partito && nome.equals(((Partito) other).nome);
	}
	@Override
	public int hashCode() {
		return nome.hashCode();
	}
	@Override
	public String toString() {
		return nome;
	}
}
```
```Java
package it.univr.elezioni;
public class VotiPerPartito {
	public final Partito partito;
	public final int voti;

	public VotiPerPartito(Partito partito, int voti) {
		this.partito = partito;
		this.voti = voti;
	}
	@Override
	public String toString() {
		return partito + ": " + voti;
	}
}
```
## Esercizio libreria con [Iterator, Comparator, TreeSet]
```Java
import it.univr.library.AudioBook;
import it.univr.library.Book;
import it.univr.library.Genre;
import it.univr.library.PaperBook;
import it.univr.library.CatalogWithStatistics;
import it.univr.library.Catalog;
import java.util.Comparator;

public class Main {

	public static void main(String[] args) {
		Book jj = new PaperBook("The joy of Java", "John Stack", 2018, Genre.COMPUTING, 557);
		Book cr = new AudioBook("Cappuccetto rosso", "Charles Perrault", 1697, Genre.FICTION, 13);
		Book ps = new AudioBook("I promessi sposi", "Alessandro Manzoni", 1840, Genre.FICTION, 1223);
		Book ps2 = new PaperBook("I promessi sposi", "Alessandro Manzoni", 1840, Genre.FICTION, 622);
		Book gl = new PaperBook("Sentieri in Lessinia", "Giovanni Gamba", 2015, Genre.GUIDE, 233);
		Book sv = new PaperBook("Gli Scala di Verona", "Roberta Guidi", 2012, Genre.HISTORY, 380);
		Book sv2 = new PaperBook("Gli Scala di Verona", "Roberta Guidi", 2012, Genre.HISTORY, 380);

		System.out.println("Ordinamento naturale:");
		// TODO: crea e poi stampa un catalogo con statistiche che contiene jj, cr, ps, ps2, gl, sv, sv2, ordinato
		// secondo il compareTo fra i libri
		System.out.println(new CatalogWithStatistics(jj, cr, ps, ps2, gl, sv, sv2));

		System.out.println("Ordinamento per autore:");
		// TODO: crea e poi stampa un catalogo che contiene jj, cr, ps, ps2, gl, sv, sv2, ordinato
		// per autore e, a parita' di autore, secondo il compareTo fra i libri
		Comparator<Book> comp1 = new Comparator<Book>(){
			public int compare(Book book1, Book book2){
				int var = book1.getAuthor().compareTo(book2.getAuthor());
				if(var == 0){
					return book1.compareTo(book2);
				}
				return var;
			}
		};
		System.out.println(new Catalog(comp1 , jj, cr, ps, ps2, gl, sv, sv2));

		System.out.println("Ordinamento per anno di pubblicazione:");
		// TODO: crea e poi stampa un catalogo che contiene jj, cr, ps, ps2, gl, sv, sv2, ordinato
		// per anno e, a parita' di anno, secondo il compareTo fra i libri
		Comparator<Book> comp2 = new Comparator<Book>(){
			public int compare(Book book1, Book book2){
				int var = book1.getYear() - book2.getYear();
				if(var == 0){
					return book1.compareTo(book2);
				}
				return var;
			}
		};
		System.out.println(new Catalog(comp2, jj, cr, ps, ps2, gl, sv, sv2));

		try {
			new PaperBook("Gli Scala di Verona", "Roberta Guidi", 2012, Genre.HISTORY, -380);
			System.out.println("legale");
		}catch (IllegalArgumentException e) {
			System.out.println("illegale");
		}

		try {
			new PaperBook("Gli Scala di Verona", null, 2012, Genre.HISTORY, 380);
			System.out.println("legale");
		}catch (IllegalArgumentException e) {
			System.out.println("illegale");
		}
	}
}
```
```Java
package it.univr.library;

public abstract class Book implements Comparable<Book> {
	private String title;
	private String author;
	private int year;
	private Genre genre;
	protected Book(String title, String author, int year, Genre genre) {
		this.title = title;
		this.author = author;
		this.year = year;
		this.genre = genre;		
	}
	public String getTitle() {
		return this.title; 
	}
	public String getAuthor() {
		return this.author;
	}
	public Genre getGenre() {
		return this.genre; 
	}
	public int getYear() {
		return this.year; 
	}

	@Override
	public String toString() {
		return "\""+this.title+"\" "+this.author+" ("+this.year+", "+this.genre+")";
	}
	@Override
	public boolean equals(Object other) {
		return other instanceof Book && 
		this.title == ((Book)other).title && 
		this.author == ((Book)other).author && 
		this.year == ((Book)other).year;
	}
	@Override
	public int hashCode() {
		return this.title.hashCode()^this.author.hashCode()^this.year;
	}
	@Override
	public int compareTo(Book other) {
		int title = this.title.compareTo(other.title);
		if(title == 0){
			int author = this.author.compareTo(other.author);
			if(author == 0){
				return this.year - other.year;
			}
			return author;
		}
		return title;
	}
}
```

```Java
package it.univr.library;

import java.util.Comparator;
import java.util.Iterator;
import java.util.SortedSet;
import java.util.TreeSet;

public class Catalog implements Iterable<Book> {
	private SortedSet<Book> books;

	public Catalog(Book... books) {
		this.books = new TreeSet<Book>();
		for(Book book : books){
			this.books.add(book);
		}
	}
	public Catalog(Comparator<Book> comparator, Book... books) {
		this.books = new TreeSet<Book>(comparator);
		for(Book book: books){
			this.books.add(book);
		}
	}

	@Override
	public Iterator<Book> iterator() {
		return books.iterator();
	}
	@Override
	public String toString() {
		String s= "";
		for(Book book: books){
			s += book + "\n";
		}
		return s;
	}
}
```

```Java
package it.univr.library;
public class CatalogWithStatistics extends Catalog {

	public CatalogWithStatistics(Book... books) {
		super(books);
	}

	@Override
	public String toString() {
		String s= super.toString();
		int pages = 0;
		int minutes = 0;
		for(Book book: this){
			if(book instanceof AudioBook){
				minutes += ((AudioBook)book).getMinutes();
			} else if(book instanceof PaperBook){
				pages += ((PaperBook)book).getPages();
			}
		}
		return s+"\nThis catalog contains paper books for a total of "+pages+" pages and audiobooks for a total of "+minutes+" minutes";
	}
}
```

```Java
package it.univr.library;
public enum Genre {
	COMPUTING,
	FICTION,
	GUIDE,
	HISTORY
}
```
```Java
package it.univr.library;
public class AudioBook extends Book {
	private int minutes;

	public AudioBook(String title, String author, int year, Genre genre, int minutes) {
		super(title, author, year, genre);
		this.minutes = minutes;
	}
	public int getMinutes() {
		return this.minutes;
	}

	@Override
	public String toString() {
		return ""+super.toString()+" ["+this.minutes+" minutes]";
	}
	@Override
	public boolean equals(Object other) {
		return other instanceof AudioBook && super.equals(other);
	}
	@Override
	public int compareTo(Book other) {
		int var = super.compareTo(other);
		if(var == 0){
			if(other instanceof AudioBook){
				return 0;
			}else if(other instanceof PaperBook){
				return 1;
			}
		}
		return var;
	}
}
```
## Esercizio modellii auto con []
```Java
import it.univr.rent.Agency;
import it.univr.rent.License;
import it.univr.rent.Model;
import it.univr.rent.AbstractModel;
import it.univr.rent.Car;
import it.univr.rent.Truck;
import it.univr.rent.Bus;
import it.univr.rent.Motorbike;
import it.univr.rent.IllegalLicenseException;
import it.univr.rent.ModelNotAvailableException;

public class Main {

	public static void main(String[] args) {
		Model captur = new Car("Renault Captur", 75);
		Model panda = new Car("Fiat Panda plus", 30);
		Model iliade = new Bus("Renault Iliade", 500);
		Model pan = new Motorbike("HD Pan American", 75);
		Model pandino = new Car("Fiat Panda", 28);
		Model pandango = new Car("Fiat Panda plus", 30);
		
		Agency hartz = new Agency("Hartz", captur, iliade, panda, pan, pandino);
		System.out.println(hartz);

		for (License license: License.values())
			printModelsFor(license, hartz);
		
		hartz.rent(captur, License.C, 3);
		hartz.rent(panda, License.B, 2);
		hartz.rent(pandango, License.C, 2);
		System.out.println("\nMost rented model: " + hartz.mostRented());

		try {
			hartz.rent(iliade, License.B, 5);
		}
		catch (IllegalLicenseException e) {
			System.out.println("Cannot rent " + iliade + " with license " + License.B);
		}
	}

	private static void printModelsFor(License license, Agency agency) {
		System.out.println("Models available with license " + license);
		System.out.println(agency.modelsAvailableForLicense(license));
	}
}
```
```Java
package it.univr.rent;

import java.util.Set;
import java.util.TreeSet;
import java.util.Map;
import java.util.HashMap;

// Ci sono 5 TODO

public class Agency {

	private String name;
	private Set<Model> fleet;
	private Map<String,Integer> rented;

	public Agency(String name, Model... fleet) {
		this.name = name;
		if(fleet == null){
			throw new IllegalArgumentException("Fleet vuoto!");
		}
		this.fleet = new TreeSet<Model>();
		this.rented =  new HashMap<String,Integer>();
		for(Model model: fleet){
			this.fleet.add(model);
			this.rented.putIfAbsent(model.getName(),0);
		}
	}

	public Set<Model> modelsAvailableForLicense(License license) {
		Set<Model> returned = new TreeSet<Model>();
		for(Model model: this.fleet){
			if(model.canBeDrivenWith(license)){
				returned.add(model);
			}
		}
		return returned;
	}

	public void rent(Model model, License license, int days) {
		boolean exists = false;
		if(!model.canBeDrivenWith(license)){
			throw new IllegalLicenseException("Wrong licence.");
		}
		for(Model m: this.fleet){
			if(m.equals(model)){
				exists = true;
			}
		}
		if(!exists){
			throw new ModelNotAvailableException("Wrong model.");
		}
		this.rented.put(model.getName(), this.rented.get(model.getName())+days);
	}

	public Model mostRented() {
		int max = 0;
		Model m = null;
		for(Model model: this.fleet){
			if(max < this.rented.get(model.getName())){
				max = this.rented.get(model.getName());
				m = model;
			}
		}
		return m;
	}

	@Override
	public String toString() {
		String models = "";
		for(Model model: this.fleet){
			models += model.getName()+" "+model.pricePerDay()+" euros per day\n";
		}
		return "Agency "+this.name+
		". Available models:\n-------------------------------\n"+models;
	}
}
```
```Java
package it.univr.rent;
public abstract class AbstractModel implements Model {

	private String name;
	private int pricePerDay;

	protected AbstractModel(String name, int pricePerDay) {
		this.name = name;
		this.pricePerDay = pricePerDay;
	}

	@Override
	public final String getName() {
		return this.name;
	}
	@Override
	public final int pricePerDay() {
		return this.pricePerDay;
	}
	@Override
	public String toString() {
		return this.name;
	}
	@Override
	public final int compareTo(Model other) {
		int price = this.pricePerDay - other.pricePerDay();
		if(price == 0){
			return this.name.compareTo(other.getName());
		}
		return price;
	}

	@Override
	public abstract boolean equals(Object other);
	@Override
	public int hashCode() {
		return this.name.hashCode()^this.pricePerDay;
	}
}
```
```Java
package it.univr.rent;
public interface Model extends Comparable<Model> {
	public String getName();
	public boolean canBeDrivenWith(License license);
	public int pricePerDay();
}
```
```Java
package it.univr.rent;

public class Car extends AbstractModel {
    public Car(String name, int pricePerDay){
        super(name, pricePerDay);
    }
    
    public boolean equals(Object other){
        return other instanceof Car &&
            this.getName() == ((Car)other).getName()&&
            this.pricePerDay() == ((Car)other).pricePerDay();
    }

    public boolean canBeDrivenWith(License license){
        return license != license.A;
    }
}

```
```Java
package it.univr.rent;

public enum License {
	A, // per moto (Motorbike)
	B, // per moto e automobili (Car)
	C, // per moto, automobili e camion (Truck)
	D  // per moto, automobili e autobus (Bus)
}
```

```Java
package it.univr.rent;
public class IllegalLicenseException extends IllegalArgumentException{
    public IllegalLicenseException(String message){
        super(message);
    }
}

```