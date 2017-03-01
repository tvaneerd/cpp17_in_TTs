namespace std::filesystem
---

<table>
<tr>
<th>
C++14 Windows
</th>
<th>
C++17
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
#include &lt;windows.h&gt;

void copy_foobar() {
  std::wstring dir = L"\\sandbox";
  std::wstring p = dir + L"\\foobar.txt";
  std::wstring copy = p;
  copy += ".bak";
  CopyFile(p, copy, false);
  
  std::string dir_copy = dir + ".bak";
  SHFILEOPSTRUCT s = { 0 };
  s.hwnd = someHwndFromSomewhere;
  s.wFunc = FO_COPY;
  s.fFlags = FOF_SILENT;
  s.pFrom = dir.c_str();
  s.pTo = dir_copy.c_str();
  SHFileOperation(&amp;s);
}

void display_contents(std::wstring const &amp; p) {
  std::cout &lt;&lt; p &lt;&lt; "\n";

  std::wstring search = p + "\\*";
  WIN32_FIND_DATA ffd;
  HANDLE hFind =
        FindFirstFile(search.c_str(), &amp;ffd);
  if (hFind == INVALID_HANDLE_VALUE)
     return;
  
  do {
    if ( ffd.dwFileAttributes
         &amp; FILE_ATTRIBUTE_DIRECTORY) {
      std::cout &lt;&lt; "  " &lt;&lt; ffd.cFileName &lt;&lt; "\n";
    } else {
      LARGE_INTEGER filesize;
      filesize.LowPart = ffd.nFileSizeLow;
      filesize.HighPart = ffd.nFileSizeHigh;
      std::cout &lt;&lt; "  " &lt;&lt; ffd.cFileName
                &lt;&lt; " [" &lt;&lt; filesize.QuadPart
                &lt;&lt; " bytes]\n";
    }
  } while (FindNextFile(hFind, &amp;ffd) != 0);
}
</pre>
</td>
<td  valign="top" rowspan="3">
<pre lang="cpp">
#include &lt;filesystem&gt;
#include &lt;iostream&gt;
namespace fs = std::filesystem;

void copy_foobar() {
  fs::path dir = "/";
  dir /= "sandbox";
  fs::path p = dir / "foobar.txt";
  fs::path copy = p;
  copy += ".bak";
  fs::copy(p, copy);
  fs::path dir_copy = dir;
  dir += ".bak";
  fs::copy(dir, dir_copy, fs::copy_options::recursive);
}

void display_contents(fs::path const &amp; p) {
  std::cout &lt;&lt; p.filename() &lt;&lt; "\n";

  if (!fs::is_directory(p))
    return;

  for (auto const &amp; e: fs::directory_iterator{p}) {
    if (fs::is_regular_file(e.status())) {
      std::cout &lt;&lt; "  " &lt;&lt; e.path().filename()
                &lt;&lt; " [" &lt;&lt; fs::file_size(e) &lt;&lt; " bytes]\n";
    } else if (fs::is_directory(e.status())) {
      std::cout &lt;&lt; "  " &lt;&lt; e.path().filename() &lt;&lt; "\n";
    }
  }
}
</pre>
</td>
</tr>
<tr>
<th>
C++14 POSIX
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
#include &lt;dirent.h&gt;
#include &lt;sys/stat.h&gt;
#include &lt;sys/types.h&gt;

void copy_foobar() {

// [TODO]
// to copy file, use fread / fwrite

// how to copy directory...?
}

void display_contents(std::string const &amp; p) {
  std::cout &lt;&lt; p &lt;&lt; "\n";

  struct dirent *dp;
  DIR *dfd;
  
  if ((dfd = opendir(p.c_str()) == nullptr)
    return;

  while((dp = readdir(dfd)) != nullptr) {
    struct stat st;
    string filename = p + "/" + dp->d_Name;
    if (stat(filename.c_str(), &amp;st) == -1)
      continue;
      
    if ((st.st_mode &amp; S_IFMT) == S_IFDIR)
      std::cout &lt;&lt; "  " &lt;&lt; filename &lt;&lt; "\n";
    } else {
      std::cout &lt;&lt; "  " &lt;&lt; filename
                &lt;&lt; " [" &lt;&lt; st.st_size
                &lt;&lt; " bytes]\n";
    }
  }
}
</pre>
</td>
</tr>

</table>
