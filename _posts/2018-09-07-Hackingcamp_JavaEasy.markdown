---
layout: post
title:  "Hackingcamp 2018 JavaEasy"
subtitle: "Hackingcamp 2018 JavaEasy 풀이 입니다."
date:   2018-09-07 15:16:00 +0900
categories: ['ctfs']
comments: true
---

## 풀이

바이너리를 다운받아 파일을 확인하면 PE32 EXE 임을 확인할 수 있다.

{% highlight bash %}
file JavaEasy.exe
JavaEasy.exe: PE32 executable (GUI) Intel 80386 (stripped to external PDB), for MS Windows
{% endhighlight %}

파일 명에서 Java라고 힌트를 주고있고, 자바는 디컴파일이 가능하다는 것을 알아야한다.

따라서, exe 파일에서 java 파일을 추출해야 한다.

`unzip`을 이용해 exe 파일을 압축해제하면 아래와 같이 class 파일을 얻을 수 있다.


{% highlight bash %}
unzip JavaEasy.exe
Archive:  JavaEasy.exe
warning [JavaEasy.exe]:  32256 extra bytes at beginning or within zipfile
  (attempting to process anyway)
  inflating: META-INF/MANIFEST.MF
   creating: kr/
   creating: kr/kshgroup/
   creating: kr/kshgroup/easyjava/
  inflating: kr/kshgroup/easyjava/EntryMain$1.class
  inflating: kr/kshgroup/easyjava/EntryMain.class
{% endhighlight %}

EntryMain.class 파일을 디컴파일하면 아래와 같이 소스코드를 확인 할 수 있다.

{% highlight java %}
package kr.kshgroup.easyjava;

import java.awt.Color;
import java.awt.Dimension;
import java.awt.Font;
import java.awt.Toolkit;
import java.awt.event.KeyEvent;
import java.awt.event.KeyListener;
import java.util.Arrays;
import java.util.Base64;
import java.util.Base64.Encoder;
import javax.swing.JFrame;
import javax.swing.JLabel;

public class EntryMain extends JFrame
{
  private JLabel j;
  private final int max_len = 30;
  private String input = "";
  private byte[] encoded;

  public EntryMain()
  {
    Dimension d = Toolkit.getDefaultToolkit().getScreenSize();

    setTitle("Easy Java!");
    setBounds(0, 0, d.width, d.height);

    setBackground(Color.BLACK);

    this.j = new JLabel("");
    this.j.setFont(new Font(this.j.getFont().getName(), this.j.getFont().getStyle(), 30));
    this.j.setLocation(d.width / 2, d.height / 2);
    this.j.setHorizontalAlignment(0);

    add(this.j);

    setDefaultCloseOperation(3);

    setUndecorated(true);
    setVisible(true);

    addKeyListener(new KeyListener()
    {
      public void keyTyped(KeyEvent e)
      {
      }

      public void keyReleased(KeyEvent e)
      {
      }

      public void keyPressed(KeyEvent e) {
        char c = e.getKeyChar();
        if (EntryMain.this.input.length() >= 30) {
          if (Arrays.equals(EntryMain.this.encoded, new byte[] { 83, 69, 78, 66, 84, 86, 66, 55, 82, 86, 112, 102, 83, 109, 70, 50, 89, 86, 57, 68, 98, 50, 90, 109, 90, 87, 86, 102, 86, 68, 70, 116, 90, 86, 57, 80, 100, 48, 57, 57 })) {
            EntryMain.this.j.setText("Correct");
          } else {
            EntryMain.this.j.setText("Wrong");
            EntryMain.access$002(EntryMain.this, "");
          }
          return;
        }
        if ((Character.isLetterOrDigit(c)) || (c == '_') || (c == '{') || (c == '}')) {
          EntryMain.access$002(EntryMain.this, EntryMain.this.input + c);
          EntryMain.access$102(EntryMain.this, Base64.getEncoder().encode(EntryMain.this.input.getBytes()));
          EntryMain.this.j.setText(Base64.getEncoder().encodeToString(EntryMain.this.input.getBytes()));
        }
      }
    });
  }

  public static void main(String[] args)
  {
    new EntryMain();
  }
}
{% endhighlight %}

위 코드에서 중요한 부분은 `keyPressed` 함수에서 키 입력시 base64로 인코딩을 한다는 것이며 하드코딩된 숫자와 일치하는지 확인한다.

`83, 69, 78, 66, 84, 86, 66, 55, 82, 86, 112, 102, 83, 109, 70, 50, 89, 86, 57, 68, 98, 50, 90, 109, 90, 87, 86, 102, 86, 68, 70, 116, 90, 86, 57, 80, 100, 48, 57, 57`

이 값을 문자열로 변환하여 base64로 디코딩하면 flag를 얻을 수 있다.

{% highlight python %}
import base64

val = 83, 69, 78, 66, 84, 86, 66, 55, 82, 86, 112, 102, 83, 109, 70, 50, 89, 86, 57, 68, 98, 50, 90, 109, 90, 87, 86, 102, 86, 68, 70, 116, 90, 86, 57, 80, 100, 48, 57, 57

strings = ""

for v in val:
    strings += chr(v)

print base64.b64decode(strings)
{% endhighlight %}

`flag: HCAMP{EZ_Java_Coffee_T1me_OwO}`