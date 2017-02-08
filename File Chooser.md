# Selecciona un fichero de tipo vídeo


Lanzamos el selector con un intent

    //  Método que lanza el selector de tipo vídeo
    private void PickFile() {
           Intent intent = new Intent(Intent.ACTION_GET_CONTENT);
           intent.setType("video/*");
           intent.addCategory(Intent.CATEGORY_OPENABLE);

           try {
               startActivityForResult(
                       Intent.createChooser(intent, "Por favor, seleccione un vídeo"),
                       PICKER);
           } catch (ActivityNotFoundException ex) {
               Toast.makeText(this, "Por favor, instale un administrador de archivos", Toast.LENGTH_SHORT).show();
           }
       }


Recuperamos la selección

        // Recibimos un intent con el resultado
       protected void onActivityResult(int requestCode, int resultCode, Intent data) {
           switch (requestCode) {
               case PICKER:
                   if (resultCode == RESULT_OK) {
                       String filePath="";
                       String fileName ="";
                       Cursor cursor = contentResolver.query(contentURI, null, null, null, null);
                       if(cursor==null)
                          filePath=fileName=contentURI.getPath();
                      else {}
                        filePath=cursor.getString(cursor.getColumnIndex(MediaStore.Video.VideoColumns.DATA));
                        fileName=cursor.getString(cursor.getColumnIndex(OpenableColumns.DISPLAY_NAME));
                      }
                       editTextUri.setText(filePath);
                       editTextName.setText(fileName);
                   }
                   break;
           }
       }
