---
layout: artigo
nome: CG - Duas Dimensões - 2014 2S
---
{%highlight cpp%}
 
      // Arquivos necessários do Windows e Direct3D.
      #include <windows.h>
      #include <windowsx.h>
      #include <d3d9.h>
      #include <d3dx9.h>

      // Definição de tela e macros do teclado
      #define SCREEN_WIDTH  1024
      #define SCREEN_HEIGHT 768
      #define LARGURA_IMAGEM 32 // tamanho da sprite da personagem
      #define ALTURA_IMAGEM 48
      #define KEY_DOWN(vk_code) ((GetAsyncKeyState(vk_code) & 0x8000) ? 1 : 0)
      #define KEY_UP(vk_code) ((GetAsyncKeyState(vk_code) & 0x8000) ? 0 : 1)

      // Inclusão da biblioteca D3D
      #pragma comment (lib, "d3d9.lib")
      #pragma comment (lib, "d3dx9.lib")

      // global declarations
      LPDIRECT3D9 d3d;    // the pointer to our Direct3D interface
      LPDIRECT3DDEVICE9 d3ddev;    // the pointer to the device class
      LPD3DXSPRITE d3dspt;    // the pointer to our Direct3D Sprite interface

      // sprite declarations
      LPDIRECT3DTEXTURE9 sprite1;    // the pointer to the sprite
      LPDIRECT3DTEXTURE9 sprite2;    // the pointer to the sprite
      LPDIRECT3DTEXTURE9 sprite3;    // the pointer to the sprite

      // Posição inicial do sprite da Sarah
      float posicaoX = 0;
      float posicaoY = 0;

      // Velocidade de movimentação
      float velocidade = 5;

      // Índices das imagens na sprite
      int indiceX = 0; // Utilizado para dar a idéia de movimentação
      int indiceY = 1; // Utilizado para definir a direção

      // Quantidade de frames de exibição de cada imagem
      int QTD_FRAMES = 10;
      int FRAME_ATUAL = 0;


      // function prototypes
      void initD3D(HWND hWnd); // sets up and initializes Direct3D
      void render_frame(void); // renders a single frame
      void cleanD3D(void); // closes Direct3D and releases memory

      // the WindowProc function prototype
      LRESULT CALLBACK WindowProc(HWND hWnd, UINT message, WPARAM wParam, LPARAM lParam);


      // Ponto de entrada do programa
      int WINAPI WinMain(HINSTANCE hInstance,
                         HINSTANCE hPrevInstance,
                         LPSTR lpCmdLine,
                         int nCmdShow)
      {
          HWND hWnd;
          WNDCLASSEX wc;

          ZeroMemory(&wc, sizeof(WNDCLASSEX));

          wc.cbSize = sizeof(WNDCLASSEX);
          wc.style = CS_HREDRAW | CS_VREDRAW;
          wc.lpfnWndProc = (WNDPROC)WindowProc;
          wc.hInstance = hInstance;
          wc.hCursor = LoadCursor(NULL, IDC_ARROW);
          wc.lpszClassName = L"WindowClass1";

          RegisterClassEx(&wc);

          hWnd = CreateWindowEx(NULL,
                                L"WindowClass1",
                                L"Our Direct3D Program",
                                WS_EX_TOPMOST | WS_POPUP,
                                0, 0,
                                SCREEN_WIDTH, SCREEN_HEIGHT,
                                NULL,
                                NULL,
                                hInstance,
                                NULL);

          ShowWindow(hWnd, nCmdShow);

          // Configura e inicializa o Direct3D
          initD3D(hWnd);

          // loop principal
          MSG msg;

          while(TRUE)
          {
              DWORD starting_point = GetTickCount();

              if (PeekMessage(&msg, NULL, 0, 0, PM_REMOVE))
              {
                  if (msg.message == WM_QUIT)
                      break;

                  TranslateMessage(&msg);
                  DispatchMessage(&msg);
              }

              render_frame();

          // Verifica as teclas pressionas

          // ESC
          if(KEY_DOWN(VK_ESCAPE))
              PostMessage(hWnd, WM_DESTROY, 0, 0);


          // ESQUERDA
          if(KEY_DOWN(VK_LEFT))
          {
            indiceY = 1;
            posicaoX -= velocidade;
          }

          // DIREITA
          if(KEY_DOWN(VK_RIGHT))
          {
            indiceY = 2;
            posicaoX += velocidade;
          }

          // CIMA
          if(KEY_DOWN(VK_UP))
          {
            indiceY = 3;
            posicaoY -= velocidade;
          }

          // BAIXO
          if(KEY_DOWN(VK_DOWN))
          {
            indiceY = 0;
            posicaoY += velocidade;
          }

              while ((GetTickCount() - starting_point) < 25);
          }

          // clean up DirectX and COM
          cleanD3D();

          return msg.wParam;
      }


      // this is the main message handler for the program
      LRESULT CALLBACK WindowProc(HWND hWnd, UINT message, WPARAM wParam, LPARAM lParam)
      {
          switch(message)
          {
              case WM_DESTROY:
                  {
                      PostQuitMessage(0);
                      return 0;
                  } break;
          }

          return DefWindowProc (hWnd, message, wParam, lParam);
      }


      // this function initializes and prepares Direct3D for use
      void initD3D(HWND hWnd)
      {
          d3d = Direct3DCreate9(D3D_SDK_VERSION);

          D3DPRESENT_PARAMETERS d3dpp;

          ZeroMemory(&d3dpp, sizeof(d3dpp));
          d3dpp.Windowed = TRUE;
          d3dpp.SwapEffect = D3DSWAPEFFECT_DISCARD;
          d3dpp.hDeviceWindow = hWnd;
          d3dpp.BackBufferFormat = D3DFMT_A8R8G8B8;
          d3dpp.BackBufferWidth = SCREEN_WIDTH;
          d3dpp.BackBufferHeight = SCREEN_HEIGHT;

          // create a device class using this information and the info from the d3dpp stuct
          d3d->CreateDevice(D3DADAPTER_DEFAULT,
                            D3DDEVTYPE_HAL,
                            hWnd,
                            D3DCREATE_SOFTWARE_VERTEXPROCESSING,
                            &d3dpp,
                            &d3ddev);

          D3DXCreateSprite(d3ddev, &d3dspt);    // create the Direct3D Sprite object

        

          D3DXCreateTextureFromFile(d3ddev, L"Sarah.dds", &sprite1);
        D3DXCreateTextureFromFile(d3ddev, L"desert2.png", &sprite2);

          return;
      }


      // Função para desenhar na tela
      void render_frame(void)
      {
          // Limpa a tela com a cor azul
          d3ddev->Clear(0, NULL, D3DCLEAR_TARGET, D3DCOLOR_XRGB(0, 0, 0), 1.0f, 0);

          d3ddev->BeginScene();    // begins the 3D scene

          // Desenha sprites com alpha habilitado
          d3dspt->Begin(D3DXSPRITE_ALPHABLEND);

          // Desenha as sprites
          D3DXVECTOR3 center(0.0f, 0.0f, 0.0f);
          D3DXVECTOR3 position1a(posicaoX, posicaoY, 0);
        D3DXVECTOR3 position2a(0, 0, 0);

        // Retangulo de recorte para exibir apenas a Sarah adequada
        // RECT rect = { x1 - começo, y1 - começo, x2 - fim, y2 - fim }
        RECT recorte = {
          LARGURA_IMAGEM * indiceX, //X1
          ALTURA_IMAGEM * indiceY,          //Y1
          (LARGURA_IMAGEM * indiceX) + LARGURA_IMAGEM,    //X2
          (ALTURA_IMAGEM * indiceY) + ALTURA_IMAGEM};   //Y2

        // Controle de frames para a ilusão de passos
        if(FRAME_ATUAL > QTD_FRAMES)
        {
          indiceX++;
          FRAME_ATUAL = 0;

          if(indiceX > 3)
            indiceX = 0;
        }

        FRAME_ATUAL++;

        // Desenha primeiro o deserto
        d3dspt->Draw(
          sprite2, 
          NULL, 
          ¢er, 
          &position2a, 
          D3DCOLOR_XRGB(255, 255, 255));

        // Desenha a Sarah depois
        d3dspt->Draw(
          sprite1, 
          &recorte, 
          ¢er, 
          &position1a, 
          D3DCOLOR_XRGB(255, 255, 255));


          d3dspt->End();    // Encerra o desenho de sprites
          d3ddev->EndScene();    // ends the 3D scene

          d3ddev->Present(NULL, NULL, NULL, NULL);

          return;
      }


      // this is the function that cleans up Direct3D and COM
      void cleanD3D(void)
      {
          sprite1->Release();
        sprite2->Release();
          d3ddev->Release();
          d3d->Release();

          return;
      }

{%endhighlight%}