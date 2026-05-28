# Laboratorio_Quimica_UTPL

Despues de hacer la revision del proyecto CamaraHersel. Se encontro una solucion para el Login. Abriendo el proyecto de CamaraHersel en unity, en la carpeta de Assets en LoginLab se encuentra los archivos de C# (WebService y LoginUsuario). De los cuales se tomo de referencia lo siguiente:

Para el la verificacion del correo institucional para loguearse al sistema es el siguiente metodo (lo mas probable es que pueda faltar este metodo en el WebService del Laboratorio de Quimica).

IEnumerator PostRequest(string url, string json, System.Action<byte[]> result)
{
    var uwr = new UnityWebRequest(url, "POST");

    byte[] jsonToSend = new System.Text.UTF8Encoding().GetBytes(json);

    uwr.uploadHandler = new UploadHandlerRaw(jsonToSend);
    uwr.downloadHandler = new DownloadHandlerBuffer();

    uwr.SetRequestHeader("Content-Type", "application/json");

    uwr.SetRequestHeader(
        "Authorization",
        "TOKEN"
    );

    yield return uwr.SendWebRequest();
}

El siguiente codigo es la verificacion en el LoginUsuario (revisar en el c# de LoginUsuario en caso de ya haber sido implementado):

public void IniciarSesion()
    {
        if (webService == null)
        {
            Debug.LogError("WebService no asignado en LoginUsuario (Inspector).");
            return;
        }

        Panel_Datos_Incorrectos.SetActive(false);
        Panel_Opcion_Escena.SetActive(false);

        Panel_Espera.SetActive(true);

        string user = IF_Email_Fb.text.ToLower();
        string pass = IF_Password_Fb.text;

        webService.Login(user, pass, false);
    }
    public void WebServiceCallback(bool success)
    {
        Panel_Espera.SetActive(false);

        if (success)
        {
            string username = PlayerPrefs.GetString(Storage.userNamePrefKey, "");
            string nombre = PlayerPrefs.GetString(Storage.userFirstNamePrefKey, username);

            if (DataUsers.Instance != null)
            {
                string email = username + "@utpl.edu.ec";
                DataUsers.Instance.SetUsersData(nombre, username, email);
            }

            // carga directamente la escena
            SceneManager.LoadScene("SampleScene");
        }
        else
        {
            Panel_Datos_Incorrectos.SetActive(true);
        }
    }


    public void Cerrar_Panel_Datos_Incorrectos () {
        Panel_Datos_Incorrectos.SetActive(false);
        Panel_Espera.SetActive(false);
        btnLogin.GetComponent<Button>().interactable = true;
    }
