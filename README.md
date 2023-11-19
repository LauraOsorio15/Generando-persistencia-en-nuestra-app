# Generando-persistencia-en-nuestra-app
import androidx.room.Entity;
import androidx.room.PrimaryKey;

@Entity(tableName = "mascota")
public class Mascota {
    @PrimaryKey(autoGenerate = true)
    private int id;

    private String nombre;
    private int rating;

    // Constructor, getters y setters

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getNombre() {
        return nombre;
    }

    public void setNombre(String nombre) {
        this.nombre = nombre;
    }

    public int getRating() {
        return rating;
    }

    public void setRating(int rating) {
        this.rating = rating;
    }
}
import androidx.room.Dao;
import androidx.room.Insert;
import androidx.room.Query;

import java.util.List;

@Dao
public interface MascotaDao {

    @Insert
    void insertMascota(Mascota mascota);

    @Query("SELECT * FROM mascota ORDER BY id DESC LIMIT 5")
    List<Mascota> getUltimasCincoMascotas();
}
import androidx.room.Database;
import androidx.room.RoomDatabase;

@Database(entities = {Mascota.class}, version = 1)
public abstract class MascotaDatabase extends RoomDatabase {

    public abstract MascotaDao mascotaDao();
}
import android.app.Application;

import androidx.room.Room;

public class MyApplication extends Application {

    private static MascotaDatabase database;

    @Override
    public void onCreate() {
        super.onCreate();
        database = Room.databaseBuilder(getApplicationContext(), MascotaDatabase.class, "mascota_db")
                .fallbackToDestructiveMigration()
                .build();
    }

    public static MascotaDatabase getDatabase() {
        return database;
    }
}
import android.os.Bundle;

import androidx.appcompat.app.AppCompatActivity;

import java.util.List;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // Insertar una nueva mascota en la base de datos
        insertarMascota();

        // Obtener las últimas 5 mascotas con rating
        obtenerUltimasCincoMascotas();
    }

    private void insertarMascota() {
        Mascota mascota = new Mascota();
        mascota.setNombre("Max");
        mascota.setRating(4);

        MyApplication.getDatabase().mascotaDao().insertMascota(mascota);
    }

    private void obtenerUltimasCincoMascotas() {
        List<Mascota> ultimasCincoMascotas = MyApplication.getDatabase().mascotaDao().getUltimasCincoMascotas();

        // Puedes hacer algo con las últimas 5 mascotas aquí
    }
}
