---
layout: artigo
nome: CG - Coelho - 2014 2S
---
<h3>beki.cpp</h3>
<h4>beki era o nome do vegeta em um jogo piratao de dragon ball q eu tinha pra gameboy" -FERRES, Lucas Vinicius Brandt</h4>
<p class="text-muted">Pra quem não entendeu, esse é o código do coelho</p>

{% highlight cpp %}
 	#include <string>
    #include <iostream>
    #include <Windows.h>
    #include <d3d9.h>
    #include <d3dx9.h>
    #include <math.h>

    #pragma comment (lib, "d3d9.lib")
    #pragma comment (lib, "d3dx9.lib")


    #define CUSTOMFVF (D3DFVF_XYZ | D3DFVF_NORMAL) 

    using namespace std;

    CONST UINT SCREEN_WIDTH (1024);
    CONST UINT SCREEN_HEIGHT (768);
    CONST FLOAT ASPECT_RATIO ((FLOAT)SCREEN_WIDTH/(FLOAT)SCREEN_HEIGHT);

    LPD3DXMESH nave;      
    DWORD numMateriais;
    D3DMATERIAL9* material;   
    LPDIRECT3DTEXTURE9* textura; //1-) ponteiro para textura

    std::wstring caminhoApp;

    int appRodando = 1;
    float angulo = 0;

     struct OURCUSTOMVERTEX 
     {
         float x,y,z;
         D3DVECTOR NORMAL;
     };
     
     LRESULT CALLBACK TratamentoEventos(HWND han_Wind,UINT uint_Message,WPARAM parameter1,LPARAM parameter2)
     {
         switch(uint_Message)
         {
             case WM_KEYDOWN:
             {
                 appRodando = 0;
                 break;
             }
             break;
         }
     
         return DefWindowProc(han_Wind,uint_Message,parameter1,parameter2);
     }
     
     HWND NewWindow(LPCTSTR str_Title,int int_XPos, int int_YPos, int int_Width, int int_Height)
     {
         WNDCLASSEX estruturaJanela;
     
         estruturaJanela.cbSize = sizeof(WNDCLASSEX);
         estruturaJanela.style = CS_HREDRAW | CS_VREDRAW;
         estruturaJanela.lpfnWndProc = TratamentoEventos;
         estruturaJanela.cbClsExtra = 0;
         estruturaJanela.cbWndExtra = 0;
         estruturaJanela.hInstance = GetModuleHandle(NULL);
         estruturaJanela.hIcon = NULL;
         estruturaJanela.hCursor = NULL;
         estruturaJanela.hbrBackground = GetSysColorBrush(COLOR_BTNFACE);
         estruturaJanela.lpszMenuName = NULL;
         estruturaJanela.lpszClassName = L"WindowClassName";
         estruturaJanela.hIconSm = LoadIcon(NULL,IDI_APPLICATION);
     
         RegisterClassEx(&estruturaJanela);
     
         return CreateWindowEx(WS_EX_CONTROLPARENT, L"WindowClassName", str_Title, WS_OVERLAPPED | WS_POPUP, 0, 0, int_Width, int_Height, NULL, NULL, GetModuleHandle(NULL), NULL);
     }

    void CriaLuz(LPDIRECT3DDEVICE9 placaVideo)
    {
        D3DLIGHT9 light;
        D3DMATERIAL9 material;

        ZeroMemory(&light, sizeof(light));
        light.Type = D3DLIGHT_DIRECTIONAL;    
        light.Diffuse = D3DXCOLOR(0.5f, 0.9f, 0.5f, 1.0f); //cor da luz
        light.Position = D3DXVECTOR3(0.0f, 10.0f, 40.0f);
        light.Direction = D3DXVECTOR3(0.0f, -1.0f, -1.0f);
        light.Range = 50.0f;    //tamanho do cone
        light.Phi = D3DXToRadian(80.0f);     
        light.Theta = D3DXToRadian(40.0f);    
        light.Falloff = 1.0f;    

        placaVideo->SetLight(0, &light);
        placaVideo->LightEnable(0, TRUE);

        ZeroMemory(&material, sizeof(D3DMATERIAL9));
        material.Diffuse = D3DXCOLOR(1.0f, 1.0f, 1.0f, 1.0f);
        material.Ambient = D3DXCOLOR(1.0f, 1.0f, 1.0f, 1.0f);

        placaVideo->SetMaterial(&material); 
    }
     
     LPDIRECT3DDEVICE9 InicializaPlacaVideo(HWND han_WindowToBindTo)
     {

         LPDIRECT3D9 directX;
         LPDIRECT3DDEVICE9 placaVideo;
     
         directX = Direct3DCreate9(D3D_SDK_VERSION);
         if (directX == NULL)
         {
             MessageBox(han_WindowToBindTo,L"DirectX Runtime library not installed!",L"InicializaPlacaVideo()",MB_OK);
         }
     
         D3DPRESENT_PARAMETERS parametrosApresentacao;
         ZeroMemory( ¶metrosApresentacao, sizeof(parametrosApresentacao) );
         parametrosApresentacao.Windowed = FALSE;
         parametrosApresentacao.BackBufferWidth = 1024;
         parametrosApresentacao.BackBufferHeight = 768;
         parametrosApresentacao.SwapEffect = D3DSWAPEFFECT_DISCARD;
         parametrosApresentacao.BackBufferFormat = D3DFMT_A8R8G8B8;
         parametrosApresentacao.BackBufferWidth = SCREEN_WIDTH;
         parametrosApresentacao.BackBufferHeight = SCREEN_HEIGHT;

         parametrosApresentacao.EnableAutoDepthStencil = TRUE;   
         parametrosApresentacao.AutoDepthStencilFormat = D3DFMT_D16; 
     
          if (FAILED(directX->CreateDevice(D3DADAPTER_DEFAULT, D3DDEVTYPE_HAL, han_WindowToBindTo, D3DCREATE_HARDWARE_VERTEXPROCESSING, ¶metrosApresentacao, &placaVideo)))
          {
                 if (FAILED(directX->CreateDevice(D3DADAPTER_DEFAULT, D3DDEVTYPE_REF, han_WindowToBindTo, D3DCREATE_SOFTWARE_VERTEXPROCESSING, ¶metrosApresentacao, &placaVideo)))
                 {
                     MessageBox(han_WindowToBindTo,L"Failed to create even the reference device!",L"InicializaPlacaVideo()",MB_OK);
                 }
          }

          
          LPD3DXBUFFER bufferMaterialNave; 
          std::wstring arquivo = L"bunny.x";
          std::wstring caminhoModelo;
          caminhoModelo = caminhoApp.c_str();
          caminhoModelo.append(arquivo);

          D3DXLoadMeshFromX(caminhoModelo.c_str(),    
                      D3DXMESH_SYSTEMMEM,    
                      placaVideo,    
                      NULL,    
                      &bufferMaterialNave,    
                      NULL,    
                      &numMateriais,    
                      &nave);
        
        D3DXMATERIAL* tempMaterials = (D3DXMATERIAL*)bufferMaterialNave->GetBufferPointer();

        material = new D3DMATERIAL9[numMateriais];
        textura = new LPDIRECT3DTEXTURE9[numMateriais]; //2-) Iniciamos o array

        for(DWORD i = 0; i < numMateriais; i++)    
        {
            material[i] = tempMaterials[i].MatD3D;    
            material[i].Ambient = material[i].Diffuse;    

            //3-) Carregamos o material
            try
            {
                if(FAILED(D3DXCreateTextureFromFileA(
                                                placaVideo,
                                                tempMaterials[i].pTextureFilename,
                                                &textura[i])))
                {
                    textura[i] = NULL;
                }
            }
            catch(exception)
            {
                textura[i] = NULL;
            }
        }


          placaVideo->SetRenderState(D3DRS_LIGHTING, TRUE); 
          placaVideo->SetRenderState(D3DRS_AMBIENT, D3DCOLOR_XRGB(50, 50, 50));    
          placaVideo->SetRenderState(D3DRS_ZENABLE, TRUE);    
          placaVideo->SetRenderState(D3DRS_CULLMODE, D3DCULL_NONE); 

          //6-) Filtro de Textura
            placaVideo->SetSamplerState(0, D3DSAMP_MAXANISOTROPY, 8);
            placaVideo->SetSamplerState(0, D3DSAMP_MINFILTER, D3DTEXF_ANISOTROPIC);
            placaVideo->SetSamplerState(0, D3DSAMP_MAGFILTER, D3DTEXF_LINEAR);
            placaVideo->SetSamplerState(0, D3DSAMP_MIPFILTER, D3DTEXF_LINEAR);

          CriaLuz(placaVideo);
     
         return placaVideo;
     }
     
     void DesenhaCena(LPDIRECT3DDEVICE9 placaVideo)
     {
         placaVideo->Clear(0, NULL, D3DCLEAR_ZBUFFER, D3DCOLOR_XRGB(0, 0, 0), 1.0f, 0); 
         placaVideo->Clear(0, NULL, D3DCLEAR_TARGET, D3DCOLOR_XRGB(0,0,0), 1.0f, 0);
         placaVideo->BeginScene();
     
         placaVideo->SetFVF(CUSTOMFVF);

         angulo += .38f;
         D3DXMATRIX matWorldX;
         D3DXMATRIX matWorldY;
         D3DXMATRIX matWorldTransZ;
         D3DXMatrixRotationX(&matWorldX, D3DXToRadian(-90));
         D3DXMatrixRotationY(&matWorldY, D3DXToRadian(angulo));
         D3DXMatrixTranslation(&matWorldTransZ, 0, 0, angulo);
         placaVideo->SetTransform(D3DTS_WORLD, &(matWorldX * matWorldY * matWorldTransZ));

         D3DXMATRIX matView;
         D3DXMatrixLookAtLH(
             &matView,
             &D3DXVECTOR3(0.0f, 2.0f, 500.0f), //pos da camera
             &D3DXVECTOR3(0.0f, 0.0f, 0.0f), //local p onde a camera esta olhando
             &D3DXVECTOR3(0.0f, 1.0f, 0.0f)); //vector up da camera

         placaVideo->SetTransform(D3DTS_VIEW, &matView);

         D3DXMATRIX matProjection;

         D3DXMatrixPerspectiveFovLH(
             &matProjection,
             D3DXToRadian(45),
             ASPECT_RATIO,
             1.0f,
             1000.0f);
         placaVideo->SetTransform(D3DTS_PROJECTION, &matProjection);

        for(DWORD i = 0; i < numMateriais; i++)    
        {
            placaVideo->SetMaterial(&material[i]); 
            nave->DrawSubset(i); 

            //4-) Desenhamos o material
            if(textura[i] != NULL)    
            {
                placaVideo->SetTexture(0, textura[i]);   
            }
        }
     
         placaVideo->EndScene();
         placaVideo->Present(NULL, NULL, NULL, NULL);
     }
     
     int WINAPI WinMain(HINSTANCE hInstance,HINSTANCE hPreviousInstance,LPSTR lpcmdline,int nCmdShow)
     {
         //5-) Estamos setando o diretório principal para a aplicação.
         //caminhoApp = L"C:\\Documents and Settings\\Willians\\Desktop\\FATEC\\Material\\CG\\Prox_Aula\\DirectX_Meshes_X_Texturized\\Debug\\";
         //SetCurrentDirectory(caminhoApp.c_str());

         MSG msg_Message;
     
         HWND han_Window                   = NewWindow(L"DIRECTX", 0, 0, SCREEN_WIDTH, SCREEN_HEIGHT);
         LPDIRECT3DDEVICE9 placaVideo      = InicializaPlacaVideo(han_Window);
     
         while(appRodando)
         {
             if(PeekMessage(&msg_Message,han_Window,0,0,PM_REMOVE))
             {
                 DispatchMessage(&msg_Message);
             }
             DesenhaCena(placaVideo);
         }
     
         nave->Release();
         placaVideo->Release();
         DestroyWindow(han_Window);
     
         return 0;
     }
{% endhighlight %}