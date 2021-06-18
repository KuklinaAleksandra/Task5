# Task5
работа с файлом с произвольным доступом
import java.io.File;
import java.io.FileOutputStream;
import java.io.FileInputStream;
import java.io.IOException;
import java.io.ObjectOutputStream;
import java.io.ObjectInputStream;
import java.io.RandomAccessFile;
import java.io.Serializable;
import java.util.Scanner;

public class Task3 {
	
	public static class Film implements Serializable
	{
		String Name;
		int Year;
		String State;
		String Genre;
		int Budget;
		
		Film(String name, int year, String state, String genre, int budget)
		{
			Name = name;
			Year = year;
			State = state;
			Genre = genre;
			Budget = budget;
		}
	}
	
	public static void main(String[] args) throws IOException, ClassNotFoundException {
		File fi = new File("C:\\Test\\task3_input.txt");
		File fo = new File("C:\\Test\\task3_1_output.txt"); 
		File fos = new File("C:\\Test\\task3_2_output.txt"); 
		fo.createNewFile();
		fos.createNewFile();
		RandomAccessFile wf = new RandomAccessFile(fo,"rw");
		wf.seek(wf.length());
		ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream(fos));
		ObjectInputStream ois = new ObjectInputStream(new FileInputStream(fos));
		
		
		Scanner scan = new Scanner(System.in, "cp1251");		
		System.out.print("Введите количество фильмов в файле => ");
		Film[] films = ReadFilms(scan.nextInt(), new Scanner(fi, "cp1251"));
		WriteFilms(films, wf);
		ReadSerializedFilms(WriteSerializedFilms(films, oos), ois);
	}
	
	public static Film[] ReadFilms(int filmCount, Scanner s) throws IOException
	{
		Film[] films = new Film[filmCount];
		for (int i = 0; i < filmCount; i++) {

			films[i] = new  Film(s.nextLine(), 
								Integer.parseInt(s.nextLine()), 
								s.nextLine(), 
								s.nextLine(), 
								Integer.parseInt(s.nextLine()));			
		}
		return films;
	}
	
	public static void WriteFilms(Film[] films, RandomAccessFile wf) throws IOException
	{
		for (int i = 0; i < films.length; i++) {
			if (films[i].State.startsWith("Россия")) {
				System.out.println("Записан " + films[i].Name);
				wf.writeUTF(films[i].Name);
				wf.writeInt(films[i].Year);
				wf.writeUTF(films[i].State);
				wf.writeUTF(films[i].Genre);
				wf.writeInt(films[i].Budget);
			}
		}
		wf.close();
	}
		
	public static int WriteSerializedFilms(Film[] films, ObjectOutputStream oos) throws IOException
	{
		int count = 0;
		for (int i = 0; i < films.length; i++) {
			if (films[i].State.startsWith("Россия")) {
				oos.writeObject(films[i]);
				count++;
			}
		}
		oos.flush();
		oos.close();
		return count;
	}
	
	public static void ReadSerializedFilms(int filmCount, ObjectInputStream ois) throws IOException, ClassNotFoundException
	{
		for (int i = 0; i < filmCount; i++) {
			System.out.println("считан " + ((Film)ois.readObject()).Name);
		}
		ois.close();
	}
}
