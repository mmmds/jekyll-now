---
published: true
---
Continuing analysis of application from [previous post](https://mmmds.github.io/Android-Insecure-Communication-Man-In-The-Middle-Example/) I found that sessionId is held in local file.

    private static class SessionWriterTask extends AsyncTask<Context, Void, Void>
      {
        private String sessionId;

        public SessionWriterTask(String paramString1)
        {
          this.sessionId = paramString1;
        }

        protected Void doInBackground(Context... paramVarArgs)
        {
          try
          {
            paramVarArgs = paramVarArgs[0].openFileOutput("session.txt", 0);
            paramVarArgs.write(this.sessionId.getBytes());
            paramVarArgs.close();
            return null;
          }
          catch (IOException paramVarArgs)
          {
            Log.e("SessionWriterTask", "Cannot write", paramVarArgs);
          }
        }
      }
      
File's absolute path is /data/data/com.example.app/files/session.txt. Such file can be accessed without any problem on rooted device, but on unrooted one we don't have permission to read it. I found a solution which involves making a backup and then unpacking it on a computer.

	$ adb backup -noapk com.example.app

Device prompted me for password for backup (on unencrypted devices it's possible to skip it) and then backup.ab file appeared on my computer. 
I used [Android Backup Extractor](https://sourceforge.net/projects/adbextractor/) to decrypt and unpack backup.

	$ java -jar abe.jar unpack backup.ab backup.tar b4ckupp4ssw0rd
    $ tar -xf backup.tar
   
After this step I was able to read content of session.txt file and get sessionId.

	$ cat apps/com.example.app/f/session.txt
    A163B20D3696AF6185C07CD62EBA4B05
