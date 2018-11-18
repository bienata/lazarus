unit main;

{$mode objfpc}{$H+}

interface

uses
  Classes, SysUtils, FileUtil, RackCtls, TAGraph, uEGauge, Forms, Controls,
  Graphics, Dialogs, StdCtrls, ExtCtrls, blcksock, TASeries;

const
     METER_NET_NAME : string = 'neonowy';
     METER_NET_PORT : string = '5555';

type

  { TMainForm }

  TMainForm = class(TForm)
    Button1: TButton;
    Chart1: TChart;
    Chart1LineSeries1: TLineSeries;
    CheckBox1: TCheckBox;
    Memo1: TMemo;
    Timer1: TTimer;
    VoltageEdit: TEdit;
    Label1: TLabel;
    procedure Button1Click(Sender: TObject);
    procedure CheckBox1Change(Sender: TObject);
    procedure FormCreate(Sender: TObject);
    procedure Timer1Timer(Sender: TObject);
  private
    { private declarations }
  public
    { public declarations }
    n : integer;
    clientSocket : TTCPBlockSocket;
    function executeSCPICommand ( cmd : string ) : string;
  end;

var
  MainForm: TMainForm;

implementation

{$R *.lfm}

{ TMainForm }

function TMainForm.executeSCPICommand ( cmd : string ) : string;
var
  responseBuffer : string = '';
  rx : string;
begin
     clientSocket.SendString( cmd + #10 );
     repeat
           rx := clientSocket.RecvPacket( 500 ); (* timeout, ms*)
           responseBuffer := responseBuffer + rx;
     until rx = '';
     executeSCPICommand := trim( responseBuffer );
end;

procedure TMainForm.FormCreate(Sender: TObject);
begin
     clientSocket := TTCPBlockSocket.Create;
     clientSocket.Connect( METER_NET_NAME , METER_NET_PORT );
     if clientSocket.LastError <> 0 then
     begin
        showmessage( 'blad sieci:' + inttostr( clientSocket.LastError ) );
        Application.Terminate;
     end;
     (* na dobry poczatek *)
     self.Caption := self.executeSCPICommand( '*IDN?' );
     Chart1.LeftAxis.Range.Min := -10;
     Chart1.LeftAxis.Range.Max := 10;
     Chart1LineSeries1.Clear;
     n := 0;
end;

procedure TMainForm.Button1Click(Sender: TObject);
begin
     self.Timer1Timer( nil );
end;

procedure TMainForm.CheckBox1Change(Sender: TObject);
begin
  self.Timer1.Enabled := self.CheckBox1.Checked;
end;

procedure TMainForm.Timer1Timer(Sender: TObject);
var
     v : Extended;
     s : string;
begin
     s := self.executeSCPICommand( ':MEASURE:VOLTAGE:DC?' );
     try
          v := StrToFloat( s );
          self.VoltageEdit.Text := FormatFloat ( '0000.0000', v );
          self.Memo1.Lines.Insert( 0, IntToStr(n) + ' : ' + self.VoltageEdit.Text );
          self.Chart1LineSeries1.AddXY( n, v );
          inc ( n );
          if n > 200 then
          begin
               n := 0;
               self.Memo1.Lines.Clear;
               Chart1LineSeries1.Clear;
          end;
     except
        on Exception : EConvertError do exit; (* <palmface> *)
      end;
end;

end.

