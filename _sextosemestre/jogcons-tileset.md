---
layout: artigo
nome: Jogos Para Consoles - Atividade Tile Set
---
## Atividade do TileSet

### Game1.cs
{% highlight csharp %}

using System;
using System.Collections.Generic;
using System.Linq;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Audio;
using Microsoft.Xna.Framework.Content;
using Microsoft.Xna.Framework.GamerServices;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework.Input;
using Microsoft.Xna.Framework.Media;

namespace WindowsGame1
{
    /// <summary>
    /// This is the main type for your game
    /// </summary>
    public class Game1 : Microsoft.Xna.Framework.Game
    {
        GraphicsDeviceManager graphics;
        SpriteBatch spriteBatch;
        ColocadorTile ct;

        public Game1()
        {
            graphics = new GraphicsDeviceManager(this);
            Content.RootDirectory = "Content";
        }

        /// <summary>
        /// Allows the game to perform any initialization it needs to before starting to run.
        /// This is where it can query for any required services and load any non-graphic
        /// related content.  Calling base.Initialize will enumerate through any components
        /// and initialize them as well.
        /// </summary>
        protected override void Initialize()
        {
            // TODO: Add your initialization logic here

            base.Initialize();
        }

        /// <summary>
        /// LoadContent will be called once per game and is the place to load
        /// all of your content.
        /// </summary>
        protected override void LoadContent()
        {
            // Create a new SpriteBatch, which can be used to draw textures.
            spriteBatch = new SpriteBatch(GraphicsDevice);
            ct = new ColocadorTile(Content);
            // TODO: use this.Content to load your game content here
        }

        /// <summary>
        /// UnloadContent will be called once per game and is the place to unload
        /// all content.
        /// </summary>
        protected override void UnloadContent()
        {
            // TODO: Unload any non ContentManager content here
        }

        /// <summary>
        /// Allows the game to run logic such as updating the world,
        /// checking for collisions, gathering input, and playing audio.
        /// </summary>
        /// <param name="gameTime">Provides a snapshot of timing values.</param>
        protected override void Update(GameTime gameTime)
        {
            // Allows the game to exit
            if (GamePad.GetState(PlayerIndex.One).Buttons.Back == ButtonState.Pressed)
                this.Exit();
            if (Keyboard.GetState().IsKeyDown(Keys.Escape)) this.Exit();

            if (Keyboard.GetState().IsKeyDown(Keys.S)) TileController.SalvarParaArquivo();

            //TODO: Add your update logic here
            ct.Atualizar();
            base.Update(gameTime);
        }

        /// <summary>
        /// This is called when the game should draw itself.
        /// </summary>
        /// <param name="gameTime">Provides a snapshot of timing values.</param>
        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.CornflowerBlue);
            spriteBatch.Begin();
            ct.Desenhar(spriteBatch);
            TileController.Desenhar(spriteBatch);
            spriteBatch.End();
            
            base.Draw(gameTime);
        }
    }
}

{% endhighlight %}

{% highlight csharp %}

using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using Microsoft.Xna.Framework.Content;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework;

namespace WindowsGame1
{
    [Serializable]
    class Tile
    {
        public Texture2D imagemTile;
        public Vector2 posicao;
        public string assetName;
        ContentManager cman;

        public Tile(ContentManager cm, string assetName)
        {
            cman = cm;
            imagemTile = cman.Load<Texture2D>(assetName);
            this.assetName = assetName;
            posicao = new Vector2(0, 0);
        }

        public Tile()
        {
        //Esse construtor vazio é uma anomalia da pressa de programar, perdoem-me
        }

        public void ChangeImage(string assetName)
        {
            imagemTile = cman.Load<Texture2D>(assetName);
        }
    }
}


{% endhighlight %}


### TileController.cs
{% highlight csharp %}

using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework;
using System.IO;

namespace WindowsGame1
{
    class TileController
    {
        public readonly Dictionary<int, int> grid = new Dictionary<int, int>();
        static List<Tile> tiles = new List<Tile>();

        public static void SetTile(Tile tile)
        {
            //Aquele construtor vazio existe puramente porque eu não me toquei que era só fazer Tile tileNovo = new Tile(tile.imagemTile, tile.posicao);
            Tile tileNovo = new Tile();
            tileNovo.imagemTile = tile.imagemTile;
            tileNovo.posicao = tile.posicao;
            tiles.Add(tileNovo);
        }

        public static void Desenhar(SpriteBatch sb)
        {
            foreach (Tile tile in tiles)
            {
                sb.Draw(tile.imagemTile, tile.posicao, Color.White);   
            }
        }

        static List<KeyValuePair<Vector2, string>> listaTiles = new List<KeyValuePair<Vector2, string>>();
        static void SerializaTiles() {
            foreach (Tile tile in tiles) { 
                listaTiles.Add(new KeyValuePair<Vector2,string>(tile.posicao, tile.assetName));
            }
        }



        static string dir = @"c:\Users\aluno\Desktop";
        static string arquivo = Path.Combine(dir, "savedTiles");
        public static void SalvarParaArquivo()
        {
            SerializaTiles();
            using (Stream stream = File.Open(arquivo, FileMode.Create)){
                var binaryFormatter = new System.Runtime.Serialization.Formatters.Binary.BinaryFormatter();
                binaryFormatter.Serialize(stream, listaTiles);
            }
        }

        /* Nao deu tempo de terminar :(
        public static void LerDoArquivo(){
            using (Stream stream = File.Open(arquivo, FileMode.Open)){
                var binaryFormatter = new System.Runtime.Serialization.Formatters.Binary.BinaryFormatter();
                listaTiles = (List<KeyValuePair<Vector2, string>>)binaryFormatter.Deserialize(stream);
            }
            foreach (KeyValuePair<Vector2, string> kvp in listaTiles) {
                Tile tileNovo = new Tile();
                tileNovo.posicao = kvp.Key;
                tileNovo.assetName = kvp.Value;
                tiles.Add(tileNovo);
            }
        }
         */
    }
}


{% endhighlight %}

### ColocadorTile.cs
{% highlight csharp %}

using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Input;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework.Content;

namespace WindowsGame1
{
    class ColocadorTile
    {
        Tile cursorTile;
        int step = 128;
        


        public ColocadorTile(ContentManager cm)
        {
            cursorTile = new Tile(cm, "B000M800");
        }

        public void Atualizar()
        {
            Movimentar();
            MetodoQueCapturaAsTeclasParaDecidirSeVaiInserirUmTileNaTelaOuSeVaiMudarOTileSelecionado();
        }
        

        #region mudar coisas

        KeyboardState ksAnterior;
        int valorTextura = 801;
        string nomeTextura;
        /* Existe uma forma de pegar todos os arquivos de uma pasta dentro do content no XNA, mas eu tava com preguiça na hora */
        List<string> blocoTextura = new List<string>() {"B000M800",
"B000M801",
"B000N800",
"B000N801",
"B000N802",
"B000N803",
"B000N804",
"B000N805",
"B000N806",
"B100M800",
"B100M801",
"B100M802",
"B100M803",
"B100N801",
"B100N802",
"B100N803",
"B100N804",
"B100N805",
"B100N806",
"B100N807",
"B100N808",
"B100N809",
"B100S800",
"B100S801",
"B100S810",
"B100S811",
"B101S800",
"B101S801",
"B101S810",
"B101S811",
"B102S800",
"B102S801",
"B102S802",
"B102S803",
"B102S810",
"B102S811",
"B102S812",
"B102S813",
"B1B0A800",
"B1B0B800",
"B1B0E800",
"B1B0I800",
"B1S1A800",
"B1S1B800",
"B1S1E800",
"B1S1I800",
"D000M800",
"D000M801",
"D000M802",
"D000N800",
"D000N801",
"D000N802",
"D000N803",
"D0B0A800",
"D0B0B800",
"D0B0E800",
"D0B0I800",
"D0G0A800",
"D0G0B800",
"D0G0E800",
"D0G0I800",
"D100M800",
"D1B0A800",
"D1B0B800",
"D1B0E800",
"D1B0I800",
"D1S2A800",
"D1S2B800",
"D1S2E800",
"D1S2I800",
"D200M800",
"D200M801",
"D200M802",
"D200M803",
"D200M804",
"D200M805",
"D2G0A800",
"D2G0B800",
"D2G0E800",
"D2G0I800",
"D300M800",
"D300M801",
"D300M802",
"D300M803",
"D300M804",
"D3D2A800",
"D3D2A801",
"D3D2B800",
"D3D2B801",
"D3D2E800",
"D3D2E801",
"D3D2I800",
"D3D2I801",
"G000M800",
"G000M801",
"G000M802",
"G000M803",
"G0G0A800",
"G0G0A801",
"G0G0B800",
"G0G0B801",
"G0G0E800",
"G0G0E801",
"G0G0I800",
"G0G0I801",
"G0S0A800",
"G0S0B800",
"G0S0B801",
"G0S0E800",
"G0S0I800",
"G100M800",
"G1G0A800",
"G1G0B800",
"G1G0E800",
"G1G0I800",
"G200M800",
"G200M801",
"G200M803",
"G2G0A800",
"G2G0B800",
"G2G0E800",
"G2G0I800",
"G300M800",
"G3G0A800",
"G3G0B800",
"G3G0E800",
"G3G0I800",
"P000M800",
"P000M801",
"P000M802",
"P000M803",
"P000M804",
"P000M805",
"P000M806",
"P000M807",
"P0G0A800",
"P0G0B800",
"P0G0E800",
"P0G0I800",
"P100M800",
"P100M801",
"P100M802",
"P100M803",
"P100M804",
"P100M805",
"P100M806",
"P1G0A800",
"P1G0A801",
"P1G0B800",
"P1G0B801",
"P1G0E800",
"P1G0E801",
"P1G0I800",
"P1G0I801",
"R000M800",
"R0D0A800",
"R0D0A801",
"R0D0B800",
"R0D0B801",
"R0D0C800",
"R0D0C801",
"R0D0E800",
"R0D0E801",
"R0D0F800",
"R0D0F801",
"R0D0I800",
"R0D0I801",
"R0D0J800",
"R0D0J801",
"S000M800",
"S100M800",
"S200M800",
"S200M801",
"S200M802",
"S200N800",
"S200N801",
"S200N802",
"S200N803",
"S200N804",
"S2D0A800",
"S2D0A801",
"S2D0B800",
"S2D0B801",
"S2D0E800",
"S2D0E801",
"S2D0I800",
"S2D0I801",
"S2S2A800",
"S2S2A801",
"S2S2B800",
"S2S2B801",
"S2S2E800",
"S2S2E801",
"S2S2I800",
"S2S2I801",
"S300M800",
"S300M802",
"S3G0A800",
"S3G0B800",
"S3G0E800",
"S3G0I800",
"S400M800",
"S400M801",
"S4D0A800",
"S4D0A801",
"S4D0A802",
"S4D0B800",
"S4D0B801",
"S4D0B802",
"S4D0E800",
"S4D0I800",
"S4G0A800",
"S4G0A801",
"S4G0A802",
"S4G0B800",
"S4G0B801",
"S4G0B802",
"S4G0E800",
"S4G0E801",
"S4G0E802",
"S4G0I800",
"S4G0I801",
"S4G0I802",
"S4S0A800",
"S4S0A801",
"S4S0A810",
"S4S0A811",
"S4S0B800",
"S4S0B801",
"S4S0B810",
"S4S0B811",
"S4S0E800",
"S4S0E801",
"S4S0E810",
"S4S0E811",
"S4S0I800",
"S4S0I801",
"S4S0I810",
"S4S0I811",
"S500M800",
"S500M801",
"S5G0A800",
"S5G0A801",
"S5G0B800",
"S5G0B801",
"S5G0E800",
"S5G0E801",
"S5G0I800",
"S5G0I801"};
        int texturaIndex = 0;

        void TrocaTextura(bool sobe) {
            if (sobe)
            {
                if (texturaIndex < blocoTextura.Count)
                {
                    texturaIndex++;
                }
                else
                {
                    texturaIndex = 0;
                }
            }
            else
            {
                if (texturaIndex > 0)
                {
                    texturaIndex--;
                }
                else
                {
                    texturaIndex = blocoTextura.Count - 1;
                }
            }
            nomeTextura = blocoTextura[texturaIndex];
        }


        void MetodoQueCapturaAsTeclasParaDecidirSeVaiInserirUmTileNaTelaOuSeVaiMudarOTileSelecionado() // Tava me sentindo programador de iOS :)
        {
            KeyboardState ks = Keyboard.GetState();
            // 
            if (ks.IsKeyDown(Keys.Space) && ksAnterior.IsKeyUp(Keys.Space))
            {
                PrintTile();
            }

            //
            if (ks.IsKeyDown(Keys.Q) && ksAnterior.IsKeyUp(Keys.Q))
            {
                TrocaTextura(true);
                cursorTile.ChangeImage(nomeTextura);
            }

            if (ks.IsKeyDown(Keys.A) && ksAnterior.IsKeyUp(Keys.A))
            {
                TrocaTextura(false);
                cursorTile.ChangeImage(nomeTextura);
            }

            if (ks.IsKeyDown(Keys.W) && ksAnterior.IsKeyUp(Keys.W)) { }

            if (ks.IsKeyDown(Keys.S) && ksAnterior.IsKeyUp(Keys.S)) { }

            ksAnterior = ks;
        }

        void PrintTile()
        {
            TileController.SetTile(cursorTile);
        }
        #endregion


        #region movimentacao

        // O pattern de manter o KeyboardState anterior pressionado por fora serve para pegar a tecla pressionada e fazer com que ela funcione apenas uma vez após pressionada. O usuário assim tem de despressioná-la (sic) para poder realizar a ação novamente.
        KeyboardState anterior;
        void Movimentar()
        {
            KeyboardState atual = Keyboard.GetState();
   
            if(atual.IsKeyDown(Keys.Down) && anterior.IsKeyUp(Keys.Down)){
                cursorTile.posicao.Y += step;
            }

            if(atual.IsKeyDown(Keys.Up) && anterior.IsKeyUp(Keys.Up)){
                cursorTile.posicao.Y -= step;
            }


            if(atual.IsKeyDown(Keys.Left) && anterior.IsKeyUp(Keys.Left)){
                cursorTile.posicao.X -= step;
            }


            if(atual.IsKeyDown(Keys.Right) && anterior.IsKeyUp(Keys.Right)){
                cursorTile.posicao.X += step;
            }

            anterior = atual;
        }


#endregion




#region draw
        public void Desenhar(SpriteBatch sb){
            sb.Draw(cursorTile.imagemTile, cursorTile.posicao, Color.White);
        }
#endregion


    }
}

{% endhighlight %}
