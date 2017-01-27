# Permisos

## Añadir permisos "a mano" a AndroidManifest.xml

Go to Android Manifest.xml and be sure to add the <uses-permission tag > inside the manifest tag but Outside of all other tags..

<manifest xlmns:android...>

 <uses-permission android:name="android.permission.INTERNET"></uses-permission>
</manifest>

[Ejemplo](http://stackoverflow.com/questions/16948537/adding-permissions-in-androidmanifest-xml-in-android-studio)

![tag for permission](https://i.stack.imgur.com/kQ3v9.png)

![add permission](https://i.stack.imgur.com/VabO6.png)

[Listado de permisos](https://developer.android.com/reference/android/Manifest.permission.html)

[Tipos de permisos y cómo crear nuestros propios permisos](https://developer.android.com/guide/topics/security/permissions.html#normal-dangerous)

A partir de Android 6 no es suficiente con incluir los permisos en el fichero AndroidManifest.xml, si no que además hay que solicitar el permiso al usuario en tiempo de ejecución.

[Un ejemplo de cómo hacerlo](https://developer.android.com/training/permissions/requesting.html)

* Primero comprobamos si tenemos ya permiso y si no lo pedimos (Modo asíncrono)



      // Here, thisActivity is the current activity
      if (ContextCompat.checkSelfPermission(thisActivity,
                      Manifest.permission.READ_CONTACTS)
              != PackageManager.PERMISSION_GRANTED) {

          // Should we show an explanation?
          if (ActivityCompat.shouldShowRequestPermissionRationale(thisActivity,
                  Manifest.permission.READ_CONTACTS)) {

              // Show an expanation to the user *asynchronously* -- don't block
              // this thread waiting for the user's response! After the user
              // sees the explanation, try again to request the permission.

          } else {

              // No explanation needed, we can request the permission.

              ActivityCompat.requestPermissions(thisActivity,
                      new String[]{Manifest.permission.READ_CONTACTS},
                      MY_PERMISSIONS_REQUEST_READ_CONTACTS);

              // MY_PERMISSIONS_REQUEST_READ_CONTACTS is an
              // app-defined int constant. The callback method gets the
              // result of the request.
          }
      }

* Recibimos la respuesta del usuario

      @Override
      public void onRequestPermissionsResult(int requestCode,
              String permissions[], int[] grantResults) {
          switch (requestCode) {
              case MY_PERMISSIONS_REQUEST_READ_CONTACTS: {
                  // If request is cancelled, the result arrays are empty.
                  if (grantResults.length > 0
                      && grantResults[0] == PackageManager.PERMISSION_GRANTED) {

                      // permission was granted, yay! Do the
                      // contacts-related task you need to do.

                  } else {

                      // permission denied, boo! Disable the
                      // functionality that depends on this permission.
                  }
                  return;
              }

              // other 'case' lines to check for other
              // permissions this app might request
          }
      }

* Opciones para evita usar permisos: un simple Intent

Ejemplo completo:

      package com.foc.pmdm.u2.testsms;

      import android.Manifest;
      import android.content.Intent;
      import android.content.pm.PackageManager;
      import android.support.v4.app.ActivityCompat;
      import android.support.v4.content.ContextCompat;
      import android.support.v7.app.AppCompatActivity;
      import android.os.Bundle;
      import android.telephony.SmsManager;
      import android.widget.Toast;

      import java.util.ArrayList;

      public class MainActivity extends AppCompatActivity {



         // Listado de permisos https://developer.android.com/reference/android/Manifest.permission.html

          // Permisos agrupados https://developer.android.com/guide/topics/security/permissions.html
          @Override
          protected void onCreate(Bundle savedInstanceState) {
              super.onCreate(savedInstanceState);
              setContentView(R.layout.activity_main);
          }


          // Método que necesita permiso para recibir MMS
          public void ReceiveMMS()
          {
              if (ContextCompat.checkSelfPermission(this, Manifest.permission.RECEIVE_MMS) == PackageManager.PERMISSION_GRANTED) {
                      // Hago el proceso de recepción
              }
              else {
                  ActivityCompat.requestPermissions(this,
                          new String[]{Manifest.permission.RECEIVE_MMS},
                          ID_PETICION_PERMISOS_RECEIVE_MMS);
              }
          }

          public void sendSMSbyIntent(String sNumber,String sTexto)
          {   //No necesitamos permisos
              Intent sendIntent=new Intent(Intent.ACTION_VIEW);
              sendIntent.putExtra("sms_body",sTexto);
              sendIntent.putExtra("address",sNumber);
              sendIntent.setType("vnd.android-dir/mms-sms");
              startActivity(sendIntent);

          }
          // Usamos estas constantes para saber a qué petición de permisos nos estamos refiriendo
          static final int ID_PETICION_PERMISOS_SEND_SMS=76567; // Es la forma de crear una constante en Java, al ser final no cambia y static para evitar que se tenga que crear en cada instanacia
          static final int ID_PETICION_PERMISOS_RECEIVE_MMS=76577;
          public void sendSMSbyCode()
          { // Necesito los permisos

              if (ContextCompat.checkSelfPermission(this, Manifest.permission.SEND_SMS) == PackageManager.PERMISSION_GRANTED)
              {
                  String sTexto="Texto"; // Lo obtendríamos de un EditText o similar
                  String sNumber="+00000000000"; // Lo obtendríamos de un EditText o similar
                  SmsManager smsManager = SmsManager.getDefault();
                  ArrayList<String> list = smsManager.divideMessage(sTexto);
                  for (String sFragmento : list) {
                      smsManager.sendTextMessage(sNumber, null, sFragmento, null, null);
                  }

              }
              else {

                  ActivityCompat.requestPermissions(this,
                          new String[]{Manifest.permission.SEND_SMS},
                          ID_PETICION_PERMISOS_SEND_SMS);
              }
          }

          @Override
          public void onRequestPermissionsResult(int requestCode, String permissions[], int[] grantResults) {

              switch(requestCode)
              {
                  case ID_PETICION_PERMISOS_SEND_SMS:
                      if(grantResults[0]==PackageManager.PERMISSION_GRANTED)
                      {
                         // Ahora hacemos el envio de SSM
                          sendSMSbyCode();
                      }
                      else
                      {
                          // No han dado permiso avisamos de que no se puede hacer nada
                          Toast.makeText(this,"No se tiene permiso",Toast.LENGTH_LONG).show();
                      }
                      break;
                  case ID_PETICION_PERMISOS_RECEIVE_MMS:
                      if(grantResults[0]==PackageManager.PERMISSION_GRANTED)
                      {
                          ReceiveMMS();
                      }
                      else
                      {
                          // No han dado permiso avisamos de que no se puede hacer nada
                          Toast.makeText(this,"No se tiene permiso",Toast.LENGTH_LONG).show();
                      }

                      break;
              }
          }

      }
