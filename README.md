
# Ex No: 1 A : To create a employee details fields and to display the employee details using Firebase Database in Android Studio.

## NAME : AHAMED SAHUL HAMEED M
## REGISTER NUMBER : 212224040016


## AIM:

To create and display the employee details using Firebase Database in Android Studio.

## EQUIPMENTS REQUIRED:

Android Studio(Min.required Artic Fox)

## ALGORITHM:

Step 1: Open Android Stdio and then click on File -> New -> New project.

Step 2: Then type the Application name as HelloWorld and click Next. 

Step 3: Then select the Minimum SDK as shown below and click Next.

Step 4: Then select the Empty Activity and click Next. Finally click Finish.

Step 5: Design layout in activity_main.xml.

Step 6: Display the employee details in MainActivity file.

Step 7: Save and run the application.

## PROGRAM:
```
Program to print the DatabaseTable using the firebasedatabase”.
Developed by: Ahamed Sahul Hameed M
Registeration Number : 212224040016
```
## Emp.java : 
```java

package com.example.pmd1;

import androidx.annotation.Keep;

@Keep
public class Emp {
    public String id;
    public String name;
    public String age;
    public String salary;

    public Emp() {
        // Required for Firebase Realtime Database
    }

    public Emp(String name, String age, String salary) {
        this.name = name;
        this.age = age;
        this.salary = salary;
    }

    public Emp(String id, String name, String age, String salary) {
        this.id = id;
        this.name = name;
        this.age = age;
        this.salary = salary;
    }
}

```

## MainActivity.java : 
```java
package com.example.pmd1;

import android.os.Bundle;
import android.widget.*;
import androidx.appcompat.app.AppCompatActivity;
import com.google.firebase.database.*;
import java.util.*;

public class MainActivity extends AppCompatActivity {

    EditText etName, etAge, etSalary;
    Button btnAdd, btnUpdate, btnDelete;
    ListView listView;
    DatabaseReference dbRef;

    ArrayList<String> items = new ArrayList<>();
    ArrayList<String> keys = new ArrayList<>();
    ArrayAdapter<String> adapter;
    String selectedKey = null;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        etName = findViewById(R.id.etName);
        etAge = findViewById(R.id.etAge);
        etSalary = findViewById(R.id.etSalary);

        btnAdd = findViewById(R.id.btnAdd);
        btnUpdate = findViewById(R.id.btnUpdate);
        btnDelete = findViewById(R.id.btnDelete);
        listView = findViewById(R.id.listView);

        dbRef = FirebaseDatabase.getInstance()
                .getReference("Employees");

        adapter = new ArrayAdapter<>(
                this,
                android.R.layout.simple_list_item_1,
                items
        );

        listView.setAdapter(adapter);

        dbRef.addValueEventListener(new ValueEventListener() {
            @Override
            public void onDataChange(DataSnapshot snapshot) {
                items.clear();
                keys.clear();

                for (DataSnapshot d : snapshot.getChildren()) {
                    Emp e = d.getValue(Emp.class);

                    if (e != null) {
                        items.add(
                                e.name + " | " + e.age + " | " + e.salary
                        );

                        keys.add(d.getKey());
                    }
                }

                adapter.notifyDataSetChanged();
            }

            @Override
            public void onCancelled(DatabaseError error) {
            }
        });

        btnAdd.setOnClickListener(v -> {
            Emp e = new Emp(
                    etName.getText().toString(),
                    etAge.getText().toString(),
                    etSalary.getText().toString()
            );

            dbRef.push().setValue(e);
            clearFields();
        });

        btnUpdate.setOnClickListener(v -> {
            if (selectedKey != null) {
                Emp e = new Emp(
                        etName.getText().toString(),
                        etAge.getText().toString(),
                        etSalary.getText().toString()
                );

                dbRef.child(selectedKey).setValue(e);
                clearFields();
            } else {
                Toast.makeText(
                        this,
                        "Select an item first",
                        Toast.LENGTH_SHORT
                ).show();
            }
        });

        btnDelete.setOnClickListener(v -> {
            if (selectedKey != null) {
                dbRef.child(selectedKey).removeValue();
                clearFields();
            } else {
                Toast.makeText(
                        this,
                        "Select an item first",
                        Toast.LENGTH_SHORT
                ).show();
            }
        });

        listView.setOnItemClickListener((p, v, pos, id) -> {
            selectedKey = keys.get(pos);

            dbRef.child(selectedKey).get()
                    .addOnSuccessListener(d -> {
                        Emp e = d.getValue(Emp.class);

                        if (e != null) {
                            etName.setText(e.name);
                            etAge.setText(e.age);
                            etSalary.setText(e.salary);
                        }
                    });
        });
    }

    private void clearFields() {
        etName.setText("");
        etAge.setText("");
        etSalary.setText("");
        selectedKey = null;
    }
}

```
## activity_main.xml :
```xml

<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <EditText
        android:id="@+id/etName"
        android:layout_width="0dp"
        android:layout_height="55dp"
        android:hint="Name"
        android:inputType="textPersonName"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent" />

    <EditText
        android:id="@+id/etAge"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:hint="Age"
        android:inputType="number"
        app:layout_constraintTop_toBottomOf="@id/etName"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent" />

    <EditText
        android:id="@+id/etSalary"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:hint="Salary"
        android:inputType="numberDecimal"
        app:layout_constraintTop_toBottomOf="@id/etAge"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent" />

    <Button
        android:id="@+id/btnAdd"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:text="Add"
        app:layout_constraintTop_toBottomOf="@id/etSalary"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toStartOf="@id/btnUpdate"
        app:layout_constraintHorizontal_weight="1" />

    <Button
        android:id="@+id/btnUpdate"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:text="Update"
        app:layout_constraintTop_toBottomOf="@id/etSalary"
        app:layout_constraintStart_toEndOf="@id/btnAdd"
        app:layout_constraintEnd_toStartOf="@id/btnDelete"
        app:layout_constraintHorizontal_weight="1" />

    <Button
        android:id="@+id/btnDelete"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:text="Delete"
        app:layout_constraintTop_toBottomOf="@id/etSalary"
        app:layout_constraintStart_toEndOf="@id/btnUpdate"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_weight="1" />

    <ListView
        android:id="@+id/listView"
        android:layout_width="0dp"
        android:layout_height="0dp"
        android:layout_marginTop="12dp"
        app:layout_constraintTop_toBottomOf="@id/btnAdd"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
*/
```

## OUTPUT

<img width="1917" height="1078" alt="Screenshot 2026-10-29 100838" src="https://github.com/user-attachments/assets/cc8ffb26-28b6-4db2-955e-55177b3c4d47" />

<img width="1917" height="1078" alt="Screenshot 2026-10-29 101109" src="https://github.com/user-attachments/assets/918daa37-c440-4eff-afae-1cc2df2b1815" />



## RESULT
Thus a Simple Android Application create a firebase database and to display the employee details using Firbase Real Time Database in Android Studio is developed and executed successfully.
