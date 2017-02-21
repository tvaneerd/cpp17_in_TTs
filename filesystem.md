namespace std::filesystem
---

<table>
<tr>
<th>
C++14
</th>
<th>
C++17
</th>
</tr>
<tr>
<td  valign="top">
<pre lang="cpp">
#include <windows.h>

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
  SHFileOperation(&s);
}

void display_contents(std::wstring const &amp; p) {
  std::cout << p << "\n";

  std::wstring search = p + "\\*";
  WIN32_FIND_DATA ffd;
  HANDLE hFind = FindFirstFile(search.c_str(), &amp;ffd);
  if (hFind == INVALID_HANDLE_VALUE)
     return;
  
  do {
    if (ffd.dwFileAttributes & FILE_ATTRIBUTE_DIRECTORY) {
      std::cout << "  " << ffd.cFileName << "\n";
    } else {
      LARGE_INTEGER filesize;
      filesize.LowPart = ffd.nFileSizeLow;
      filesize.HighPart = ffd.nFileSizeHigh;
      std::cout << "  " << ffd.cFileName
                << " [" << filesize.QuadPart << " bytes]\n";
    }
  } while (FindNextFile(hFind, &amp;ffd) != 0);
}
</pre>
</td>
<td  valign="top">
<pre lang="cpp">
#include <experimental/filesystem>
#include <iostream>
namespace fs = std::experimental::filesystem;

void copy_foobar() {
  fs::path dir = "/";
  dir /= "sandbox";
  fs::path p = dir / "foobar.txt";
  fs::path copy = p;
  copy += ".bak";
  fs::copy(p, copy);
  fs::path dir_copy = dir + ".bak";
  fs::copy(dir, dir_copy, fs::copy_options::recursive);
}

void display_contents(fs::path const& p) {
  std::cout << p.filename() << "\n";

  if (!fs::is_directory(p))
    return;

  for (auto const& e: fs::directory_iterator{p}) {
    if (fs::is_regular_file(e.status())) {
      std::cout << "  " << e.path().filename()
                << " [" << fs::file_size(e) << " bytes]\n";
    } else if (fs::is_directory(e.status())) {
      std::cout << "  " << e.path().filename() << "\n";
    }
  }
}
</pre>
</td>
</tr>
</table>
