# PicknCrop2
An open source project for the new learners on android platform. The source is of a simple app that implements the following: <br>
1> How to pick an image from the gallery. <br>
2> Scaling down the resolution of the image to fit into the imageview frame. <br>
3> Making a crop using com.android.camera.action.CROP <br>
4> Making the corners of the image rounded. <br>


##<u>PICK AN IMAGE FROM THE GALERY AND SCALE DOWN</u>
```java
    public void loadImageFromGallery(View view) {
        // Create intent to Open Image applications like Gallery, Google Photos
        Intent galleryIntent = new Intent(Intent.ACTION_PICK,
                MediaStore.Images.Media.EXTERNAL_CONTENT_URI);
        // Start the Intent
        startActivityForResult(galleryIntent, RESULT_LOAD_IMG);
    }
```

The fuction here creates an intent to open image application and use ```java Intent.ACTION_PICK ``` to pick the image from the oppened gallery 
application. ``` startActivityForResult(galleryIntent, RESULT_LOAD_IMG); ```  this function handles the intent (any intent
specified to it) so generated from the pick event and triggers ``` onActivityResult(int requestCode, int resultCode, Intent data)```
which handles the results or actions to be performed after the intent. Since several intents can happen within the activity, different 
codes ```RESULT_LOAD_IMG``` are used to identify the action to be performed on the basis of intent receivrd.<BR>

```java

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        try {
            // When an Image is picked
            if (requestCode == RESULT_LOAD_IMG && resultCode == RESULT_OK
                    && null != data) {
                // Get the Image from data

                Uri selectedImage = data.getData();
                String[] filePathColumn = {MediaStore.Images.Media.DATA};

                // Get the cursor
                Cursor cursor = getContentResolver().query(selectedImage,
                        filePathColumn, null, null, null);
                // Move to first row
                cursor.moveToFirst();

                int columnIndex = cursor.getColumnIndex(filePathColumn[0]);
                imgDecodableString = cursor.getString(columnIndex);
                cursor.close();
                ImageView imgView = (ImageView) findViewById(R.id.imgView);
                // Set the Image in ImageView after decoding the String
                imgView.setImageBitmap(decodeSampledBitmapFromResource(imgDecodableString,
                        500, 500));
                Log.i(TAG,imgDecodableString);


            } else {
                Toast.makeText(this, "You haven't picked Image",
                        Toast.LENGTH_LONG).show();
            }

            //When Image is cropped;
            if (requestCode == RESULT_CROP ) {
                if(resultCode == Activity.RESULT_OK){
                    Bundle extras = data.getExtras();
                    Bitmap selectedBitmap = extras.getParcelable("data");
                    // Set The Bitmap Data To ImageView
                    ImageView imgView = (ImageView) findViewById(R.id.imgView);

                    //Round bordered Image
                    ImageConverter imageConverter = new ImageConverter();
                    Bitmap newSelectedBitmap = imageConverter.getRoundedCornerBitmap(selectedBitmap,270);

                    imgView.setImageBitmap(newSelectedBitmap);
                    imgView.setScaleType(ImageView.ScaleType.FIT_CENTER);
                }
            }
        } catch (Exception e) {
            Toast.makeText(this, "Something went wrong", Toast.LENGTH_LONG)
                    .show();
        }

    }
    
   //Function to return the bitmap of the scale downed image
    //
     public static Bitmap decodeSampledBitmapFromResource(String imgDecodableString,
                                                         int reqWidth, int reqHeight) {

        // First decode with inJustDecodeBounds=true to check dimensions
        final BitmapFactory.Options options = new BitmapFactory.Options();

        options.inJustDecodeBounds = true;
        BitmapFactory.decodeFile(imgDecodableString, options);


        // Calculate inSampleSize
        options.inSampleSize = calculateInSampleSize(options, reqWidth, reqHeight);

        // Decode bitmap with inSampleSize set
        options.inJustDecodeBounds = false;
        return BitmapFactory.decodeFile(imgDecodableString, options);
    }

```
```calculateInSampleSize(BitmapFactory.Options options, int reqWidth, int reqHeight) ``` calculates the sample size if the the image 
dimensions are greater the imageview frame capacity. It sets the  ```options.inSampleSize ``` where ``` options ``` is an 
object of  ``` BitmapFactory.Options ```
