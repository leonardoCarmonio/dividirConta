# dividirConta
Projeto Demo para o curso de férias de Introdução ao Xamarin Forms da PUC Minas Contagem - 2018.

Dividir conta é um App nativo feito em Xamarin Forms para calcular o valor que cada pessoa pagará na hora de dividir a conta do restaurante.

## Como criar um projeto Xamarin Forms no Visual Studio
![Criar Projeto Xamarin Forms](https://github.com/dayaneLima/dividirConta/blob/master/Docs/Imgs/dividirContaCriacaoProjeto.gif)

## Primeira tela

Tela com os campos para preencher o número de pessoas, valor total e a porcentagem do garçom. Há um botão para limpar os dados preenchidos e outro para calcular o 
valor total que cada pessoa pagará. Ao clicar em calcular, abrirá outra tela exibindo o resultado do cálculo.

Abaixo se encontra o código do Layout da tela:

  ```xml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DividirConta"
             x:Class="DividirConta.MainPage"
             Title="Dividir Conta"
             Padding="10">
    <StackLayout>
        <Label Text="Total de pessoas:" />
        <Entry Keyboard="Numeric"
               x:Name="TotalPessoasEntry"/>
        <Label Text="Valor total:" />
        <Entry Keyboard="Numeric"
               x:Name="ValorTotalEntry"/>
        <Label Text="Porcentagem garçom:" />
        <Entry Keyboard="Numeric"
               x:Name="PorcentagemGarcomEntry"/>      
        
        <Grid>            
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
            </Grid.ColumnDefinitions>            
            <Grid.RowDefinitions>
                <RowDefinition Height="*" />
            </Grid.RowDefinitions>
            <Button Text="Limpar"
                    Grid.Column="0"
                    Grid.Row="0" 
                    x:Name="LimparButton"/>            
            <Button Text="Calcular"
                    Grid.Column="1"
                    Grid.Row="0"
                    x:Name="CalcularButton"/>            
        </Grid>             
    </StackLayout>
</ContentPage>

  ```
  
  Agora o Code Behind responsável por validar os dados da tela e pelas ações dos botões de próximo e anterior:

```c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Xamarin.Forms;

namespace DividirConta
{
	public partial class MainPage : ContentPage
	{
        public int TotalPessoas { get; set; }
        public double ValorTotal { get; set; }
        public int PorcentagemGarcom { get; set; }

        public MainPage()
		    {
			      InitializeComponent();
            
            LimparButton.Clicked += LimparButton_Clicked;
            CalcularButton.Clicked += CalcularButton_Clicked;
        }

        private void CalcularButton_Clicked(object sender, EventArgs e)
        {
            if (IsValid())
            {
                Navigation.PushAsync(new ResultadoPage(TotalPessoas, ValorTotal, PorcentagemGarcom));
            }
        }        

        private void LimparButton_Clicked(object sender, EventArgs e)
        {
            TotalPessoasEntry.Text = string.Empty;
            ValorTotalEntry.Text = string.Empty;
            PorcentagemGarcomEntry.Text = string.Empty;
        }

        private bool IsValid()
        {
            var valido = true;
            var mensagemErro = string.Empty;

            if (int.TryParse(TotalPessoasEntry.Text, out int totalPessoas) && totalPessoas > 0)
            {
                TotalPessoas = totalPessoas;
            }
            else
            {
                valido = false;
                mensagemErro += "Total de pessoas inválido\n";
            }

            if (double.TryParse(ValorTotalEntry.Text, out double valorTotal) && valorTotal > 0)
            {
                ValorTotal = valorTotal;
            }
            else
            {
                valido = false;
                mensagemErro += "Valor total inválido\n";
            }

            if (!string.IsNullOrEmpty(PorcentagemGarcomEntry.Text))
            {
                if (int.TryParse(PorcentagemGarcomEntry.Text, out int porcentagemGarcom))
                {
                    PorcentagemGarcom = porcentagemGarcom;
                }
                else
                {
                    valido = false;
                    mensagemErro += "Porcentagem do garçom inválido\n";
                }
            }

            if (!valido)
            {
                DisplayAlert("Ops",mensagemErro,"Ok");
            }

            return valido;
        }
    }
}
```

## Como criar uma page no Visual Studio

![Criar Page](https://github.com/dayaneLima/dividirConta/blob/master/Docs/Imgs/dividirContaCriarPage.gif)

## Link para baixar o FontAwesome (Baixar versão web)

<a href="https://fontawesome.com"  target="_blank"> FontAwesome</a>

## Adicionar Font Awesome no projeto

![Adicionar Font Awesome](https://github.com/dayaneLima/dividirConta/blob/master/Docs/Imgs/dividirContaAddFontAwesome.gif)

## Segunda tela

Na segunda tela é feito o cálculo do valor pago por pessoa e exibido na tela. Abaixo se encontra o XAML:

 ```xml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DividirConta.ResultadoPage"
             Title="Resultado">
    <ContentPage.Content>
        <StackLayout HorizontalOptions="CenterAndExpand"
                        VerticalOptions="CenterAndExpand">            
            <StackLayout Orientation="Horizontal">
                <Label Text="&#xf1ec;" FontSize="Large">
                    <Label.FontFamily>
                        <OnPlatform x:TypeArguments="x:String" 
                                    Android="fa-solid-900.ttf#Font Awesome 5 Free Regular" 
                                    iOS="Font Awesome 5 Free"/>
                    </Label.FontFamily>
                </Label>
                <Label FontSize="Large"
                       x:Name="ValorPorPessoa"/>                
            </StackLayout>            
        </StackLayout>
    </ContentPage.Content>
</ContentPage>

  ```
  
  Agora o Code Behind responsável pelo cálculo e atribuir o valor total ao elemento da tela:
  
  ```c# 
namespace DividirConta
{
	[XamlCompilation(XamlCompilationOptions.Compile)]
	public partial class ResultadoPage : ContentPage
	{
		public ResultadoPage (int totalPessoas, double valorTotal, int porcentagemGarcom)
		{
			InitializeComponent ();

            CalcularValorPorPessoa(totalPessoas, valorTotal, porcentagemGarcom);
        }

        private void CalcularValorPorPessoa(int totalPessoas, double valorTotal, int porcentagemGarcom)
        {
            var valorGarcom = porcentagemGarcom > 0 ? (valorTotal * porcentagemGarcom / 100) : 0;
            var valor = (valorTotal + valorGarcom) / totalPessoas;
            ValorPorPessoa.Text = $"Valor por pessoa: {valor.ToString("C")}";
        }
	}
}
```

## Demo

![Criar Page](https://github.com/dayaneLima/dividirConta/blob/master/Docs/Imgs/dividirContaDemoApp.gif)

## Slides 

<a href="https://github.com/dayaneLima/dividirConta/blob/master/Docs/xamarinIntroducao.pdf"  target="_blank"> Slide da Apresentação</a>

